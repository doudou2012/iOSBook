### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-31 | Alfred Jiang | - |

### 方案名称
地图 - MKMapView 地图开发相关总结

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
MKMapView \ CLLocationCoordinate2D \ CLLocation

### 需求场景
1. （）

### 参考链接
（无）

### 详细内容

#####1. 坐标

1. CLLocationCoordinate2D 与 NSString 之间的相互转换
        + (CLLocationCoordinate2D) get2DCoordFromString:(NSString*)coordString
        {
            CLLocationCoordinate2D location;
            NSArray *coordArray = [coordString componentsSeparatedByString: @","];
            location.latitude = ((NSNumber *)coordArray[0]).doubleValue;
            location.longitude = ((NSNumber *)coordArray[1]).doubleValue;

            return location;
        }

        + (NSString *) getStringFrom2DCoord :(CLLocationCoordinate2D)location
        {
            NSString *aString = [NSString stringWithFormat:@"%f,%f",location.latitude,location.longitude];
            return aString;
        }

#####2. 大头针

1. 定义一个遵循 MKAnnotation 协议的大头针数据模型
        //
        //  REXAnnotation.swift
        //  REX
        //
        //  Created by Alfred Jiang on 3/27/15.
        //  Copyright (c) 2015 REX. All rights reserved.
        //

        import UIKit
        import MapKit

        class REXAnnotation: NSObject,MKAnnotation {

            var coordinate: CLLocationCoordinate2D
            var markerData: NSDictionary
            //自定义一个初始化方法
            init(coordinate: CLLocationCoordinate2D, markerData: NSDictionary) {
                self.markerData = markerData
                self.coordinate = coordinate
            }
        }

2. 添加到地图上
        //定义地图
        var mapViewLocation: MKMapView!

        //添加大头针
        var aLocationObject : REXAnnotation = REXAnnotation(coordinate: centerLocation, markerData : NSDictionary())
        self.mapViewLocation.addAnnotation(aLocationObject)

        //实现显示方法
        //MARK: - MapKit

        func mapView(mapView: MKMapView!, didAddAnnotationViews views: [AnyObject]!) {
            for mkaview in views
            {
                if mkaview is MKPinAnnotationView
                {
                    (mkaview as MKPinAnnotationView).pinColor = MKPinAnnotationColor.Red
                }
            }
        }

#####3. 地图以某坐标居中显示

1. MKMapView+MapViewUtil.h
        //
        //  MKMapView+MapViewUtil.h
        //  REX
        //
        //  Created by Alfred Jiang on 3/27/15.
        //  Copyright (c) 2015 REX. All rights reserved.
        //

        #import <MapKit/MapKit.h>

        @interface MKMapView (MapViewUtil)

        - (void)setCenterCoordinate:(CLLocationCoordinate2D)centerCoordinate
                          zoomLevel:(NSUInteger)zoomLevel
                           animated:(BOOL)animated;

        @end

2. MKMapView+MapViewUtil.m
        //
        //  MKMapView+MapViewUtil.m
        //  REX
        //
        //  Created by Alfred Jiang on 3/27/15.
        //  Copyright (c) 2015 REX. All rights reserved.
        //

        #import "MKMapView+MapViewUtil.h"

        #define MERCATOR_OFFSET 268435456
        #define MERCATOR_RADIUS 85445659.44705395

        @implementation MKMapView (MapViewUtil)

        #pragma mark -
        #pragma mark Map conversion methods

        - (double)longitudeToPixelSpaceX:(double)longitude
        {
            return round(MERCATOR_OFFSET + MERCATOR_RADIUS * longitude * M_PI / 180.0);
        }

        - (double)latitudeToPixelSpaceY:(double)latitude
        {
            return round(MERCATOR_OFFSET - MERCATOR_RADIUS * logf((1 + sinf(latitude * M_PI / 180.0)) / (1 - sinf(latitude * M_PI / 180.0))) / 2.0);
        }

        - (double)pixelSpaceXToLongitude:(double)pixelX
        {
            return ((round(pixelX) - MERCATOR_OFFSET) / MERCATOR_RADIUS) * 180.0 / M_PI;
        }

        - (double)pixelSpaceYToLatitude:(double)pixelY
        {
            return (M_PI / 2.0 - 2.0 * atan(exp((round(pixelY) - MERCATOR_OFFSET) / MERCATOR_RADIUS))) * 180.0 / M_PI;
        }

        #pragma mark -
        #pragma mark Helper methods

        - (MKCoordinateSpan)coordinateSpanWithMapView:(MKMapView *)mapView
                                     centerCoordinate:(CLLocationCoordinate2D)centerCoordinate
                                         andZoomLevel:(NSUInteger)zoomLevel
        {
            // convert center coordiate to pixel space
            double centerPixelX = [self longitudeToPixelSpaceX:centerCoordinate.longitude];
            double centerPixelY = [self latitudeToPixelSpaceY:centerCoordinate.latitude];

            // determine the scale value from the zoom level
            NSInteger zoomExponent = 20 - zoomLevel;
            double zoomScale = pow(2, zoomExponent);

            // scale the map’s size in pixel space
            CGSize mapSizeInPixels = mapView.bounds.size;
            double scaledMapWidth = mapSizeInPixels.width * zoomScale;
            double scaledMapHeight = mapSizeInPixels.height * zoomScale;

            // figure out the position of the top-left pixel
            double topLeftPixelX = centerPixelX - (scaledMapWidth / 2);
            double topLeftPixelY = centerPixelY - (scaledMapHeight / 2);

            // find delta between left and right longitudes
            CLLocationDegrees minLng = [self pixelSpaceXToLongitude:topLeftPixelX];
            CLLocationDegrees maxLng = [self pixelSpaceXToLongitude:topLeftPixelX + scaledMapWidth];
            CLLocationDegrees longitudeDelta = maxLng - minLng;

            // find delta between top and bottom latitudes
            CLLocationDegrees minLat = [self pixelSpaceYToLatitude:topLeftPixelY];
            CLLocationDegrees maxLat = [self pixelSpaceYToLatitude:topLeftPixelY + scaledMapHeight];
            CLLocationDegrees latitudeDelta = -1 * (maxLat - minLat);

            // create and return the lat/lng span
            MKCoordinateSpan span = MKCoordinateSpanMake(latitudeDelta, longitudeDelta);
            return span;
        }

        #pragma mark -
        #pragma mark Public methods

        - (void)setCenterCoordinate:(CLLocationCoordinate2D)centerCoordinate
                          zoomLevel:(NSUInteger)zoomLevel
                           animated:(BOOL)animated
        {
            // clamp large numbers to 28
            zoomLevel = MIN(zoomLevel, 28);

            // use the zoom level to compute the region
            MKCoordinateSpan span = [self coordinateSpanWithMapView:self centerCoordinate:centerCoordinate andZoomLevel:zoomLevel];
            MKCoordinateRegion region = MKCoordinateRegionMake(centerCoordinate, span);

            // set the region like normal
            [self setRegion:region animated:animated];
        }

        @end

3. 使用
        self.mapViewLocation.setCenterCoordinate(centerLocation,zoomLevel: 14, animated: true)


### 效果图
（无）

### 备注
（无）

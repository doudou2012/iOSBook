### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-09 | Alfred Jiang | - |

### 方案名称
JSON - 使用 JSONHelper 进行 JSON 数据解析

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
JSONHelper \ JSON \ 解析 \ 网络返回数据

### 需求场景
1. 解析由网络返回的 JSON 格式数据

### 参考链接
1. [GitHub - JSONHelper](https://github.com/isair/JSONHelper)

### 详细内容

#####1. 将 JSONHelper.swift 文件加入工程

#####2. 自定义的解析数据模型需要实现 Deserializable 协议

1. 示例一
        //定义
        internal struct Movie: Deserializable {
          var name: String? // You can also use let instead of var if you want.
          var releaseDate: NSDate?

          init(data: [String: AnyObject]) {
            name <-- data["name"]
            releaseDate <-- (data["release_date"], "yyyy-MM-dd") // Refer to the next section for more info.
          }
        }

        //使用
        AFHTTPRequestOperationManager().GET(
          "http://yoursite.com/movies/"
          parameters: nil,
          success: { operation, data in
            var movies: [Movie]?
            movies <-- data["movies"]

            if let movies = movies {
              // Response contained a movies array, and we deserialized it. Do what you want here.
            } else {
              // Server gave us a response but there was no "movies" key in it, so the movies variable
              // is equal to nil. Do some error handling here.
            }
          },
          failure: { operation, error in
            // Handle error.
        })

2. 示例二
        //
        //  User.swift
        //  REX
        //
        //  Created by Alfred Jiang on 3/19/15.
        //  Copyright (c) 2015 REX. All rights reserved.
        //

        import UIKit

        var _currentUser: User?
        var currentUserKey = "currentUser"
        var userDidLoginNotification = "userDidLoginNotification"
        var userDidLogoutNotification = "userDidLogoutNotification"

        internal struct SideStandardProfileRequest: Deserializable {
            var url: String = ""

            init(data: [String: AnyObject]) {
                url <-- data["url"]
            }
        }

        internal struct Country: Deserializable {
            var code: String = ""

            init(data: [String: AnyObject]) {
                code <-- data["code"]
            }
        }

        internal struct Location: Deserializable {
            var country: Country?
            var name: String = ""

            init(data: [String: AnyObject]) {
                name <-- data["name"]
                country <-- data["country"]
            }
        }

        class User : NSObject , Deserializable{
            var id: String!
            var firstName: String?
            var lastName: String?
            var headline: String?
            var profileImageUrl: String?
            var email: String?
            var industry: String?
            var countryCode : String?
            var locationAddress : String?
            var siteStandardProfileRequest: String?

            var data: NSDictionary?

            required init(data: [String: AnyObject])
            {
                self.data = data
                id <-- data["id"]
                firstName <-- data["firstName"]
                lastName <-- data["lastName"]
                headline <-- data["headline"]
                profileImageUrl <-- data["pictureUrl"]
                email <-- data["emailAddress"]
                industry <-- data["industry"]

                var aLocation: Location?
                aLocation <-- data["location"]
                countryCode = aLocation?.country?.code
                locationAddress = aLocation?.name

                var aSiteStandardProfileRequest : SideStandardProfileRequest?
                aSiteStandardProfileRequest <-- data["siteStandardProfileRequest"]

                siteStandardProfileRequest = aSiteStandardProfileRequest?.url
            }

            class var currentUser: User? {
                get {
                if (_currentUser == nil) {
                var data = NSUserDefaults.standardUserDefaults().objectForKey(currentUserKey) as? NSData
                if (data != nil) {
                var dict = NSJSONSerialization.JSONObjectWithData(data!, options: nil, error: nil) as NSDictionary
                _currentUser  <-- dict
                }
                }
                return _currentUser
                }
                set(user) {
                    _currentUser = user

                    if (_currentUser != nil) {
                        var data = NSJSONSerialization.dataWithJSONObject(user!.data!, options: nil, error: nil)
                        NSUserDefaults.standardUserDefaults().setObject(data, forKey: currentUserKey)
                    } else {
                        NSUserDefaults.standardUserDefaults().setObject(nil, forKey: currentUserKey)
                    }
                    NSUserDefaults.standardUserDefaults().synchronize()
                }
            }

        /*

        "{\"userID\":\"10001\",\"userName\":\"10001\",\"firstName\":\"4\",\"lastName\":\"4\",\"displayName\":null,\"email\":\"aaa@aaa.com\",\"password\":\"4\",\"address\":\"4\",\"headline\":null,\"profileImageUrl\":null,\"industry\":null,\"countryCode\":null,\"siteStandardProfileRequest\":null,\"roleId\":\"4\"}"

        */

            func toJSONDict() -> NSDictionary
            {
                let aDict : NSMutableDictionary = NSMutableDictionary()

                if let id = self.id {
                    aDict.setValue(id, forKey: "userName1")
                }

                if let firstName = self.firstName {
                    aDict.setValue(firstName, forKey: "firstName")
                }

                if let lastName = self.lastName {
                    aDict.setValue(lastName, forKey: "lastName")
                }

                if let headline = self.headline {
                    aDict.setValue(headline, forKey: "headline")
                }

                if let profileImageUrl = self.profileImageUrl {
                    aDict.setValue(profileImageUrl, forKey: "profileImageUrl")
                }

                if let email = self.email {
                    aDict.setValue(email, forKey: "email")
                }

                if let industry = self.industry {
                    aDict.setValue(industry, forKey: "industry")
                }

                if let countryCode = self.countryCode {
                    aDict.setValue(countryCode, forKey: "countryCode")
                }

                if let locationAddress = self.locationAddress {
                    aDict.setValue(locationAddress, forKey: "address")
                }

                if let siteStandardProfileRequest = self.siteStandardProfileRequest {
                    aDict.setValue(siteStandardProfileRequest, forKey: "siteStandardProfileRequest")
                }

                return aDict
            }
        }

#####3. 更多详细用法参考 [GitHub](https://github.com/isair/JSONHelper)

### 效果图
（无）

### 备注

1. 遵循 Deserializable 协议的属性应该是option类型

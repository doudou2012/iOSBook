### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-30 | Alfred Jiang | - |

### 方案名称
NSDate - 时间日期相关操作方法总结

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
NSDate \ 时间操作 \ 日期

### 需求场景
1. 需要用到日期显示和时间操作等场景时

### 参考链接
1. [GitHub - DateTools](https://github.com/MatthewYork/DateTools)

### 详细内容

######1. 时间戳相关

1. 获取当前时间戳
        + (NSTimeInterval)timeBySecond
        {
            NSDate* dat = [NSDate dateWithTimeIntervalSinceNow:0];
            return [dat timeIntervalSince1970];
        }

        + (NSString *)stringTimeBySecond
        {
            return [NSString stringWithFormat:@"%.0f", [[self class] timeBySecond]];
        }

2. NSDate 与 时间戳 的相互转换
        // 将 时间戳 转为 NSDate
        convenience init(timeIntervalSince1970 secs: NSTimeInterval)
        // 将 NSDate 转为 时间戳
        var timeIntervalSince1970: NSTimeInterval { get }

######2. 时间工具类推荐

1. NSDate+DateTools.h
        // Copyright (C) 2014 by Matthew York
        //
        // Permission is hereby granted, free of charge, to any
        // person obtaining a copy of this software and
        // associated documentation files (the "Software"), to
        // deal in the Software without restriction, including
        // without limitation the rights to use, copy, modify, merge,
        // publish, distribute, sublicense, and/or sell copies of the
        // Software, and to permit persons to whom the Software is
        // furnished to do so, subject to the following conditions:
        //
        // The above copyright notice and this permission notice shall
        // be included in all copies or substantial portions of the Software.
        //
        // THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
        // EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
        // OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
        // NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS
        // BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
        // ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
        // CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

        #ifndef DateToolsLocalizedStrings
        #define DateToolsLocalizedStrings(key) \
        NSLocalizedStringFromTableInBundle(key, @"DateTools", [NSBundle bundleWithPath:[[[NSBundle mainBundle] resourcePath] stringByAppendingPathComponent:@"DateTools.bundle"]], nil)
        #endif

        #import <Foundation/Foundation.h>

        static const long long SECONDS_IN_YEAR = 31556900;
        static const NSInteger SECONDS_IN_MONTH_28 = 2419200;
        static const NSInteger SECONDS_IN_MONTH_29 = 2505600;
        static const NSInteger SECONDS_IN_MONTH_30 = 2592000;
        static const NSInteger SECONDS_IN_MONTH_31 = 2678400;
        static const NSInteger SECONDS_IN_WEEK = 604800;
        static const NSInteger SECONDS_IN_DAY = 86400;
        static const NSInteger SECONDS_IN_HOUR = 3600;
        static const NSInteger SECONDS_IN_MINUTE = 60;
        static const NSInteger MILLISECONDS_IN_DAY = 86400000;

        @interface NSDate (DateTools)

        #pragma mark - Time Ago
        + (NSString*)timeAgoSinceDate:(NSDate*)date;
        + (NSString*)shortTimeAgoSinceDate:(NSDate*)date;
        - (NSString*)timeAgoSinceNow;
        - (NSString *)shortTimeAgoSinceNow;
        - (NSString *)timeAgoSinceDate:(NSDate *)date;
        - (NSString *)timeAgoSinceDate:(NSDate *)date numericDates:(BOOL)useNumericDates;
        - (NSString *)shortTimeAgoSinceDate:(NSDate *)date;

        #pragma mark - Date Components Without Calendar
        - (NSInteger)era;
        - (NSInteger)year;
        - (NSInteger)month;
        - (NSInteger)day;
        - (NSInteger)hour;
        - (NSInteger)minute;
        - (NSInteger)second;
        - (NSInteger)weekday;
        - (NSInteger)weekdayOrdinal;
        - (NSInteger)quarter;
        - (NSInteger)weekOfMonth;
        - (NSInteger)weekOfYear;
        - (NSInteger)yearForWeekOfYear;
        - (NSInteger)daysInMonth;
        - (NSInteger)dayOfYear;
        -(NSInteger)daysInYear;
        -(BOOL)isInLeapYear;
        - (BOOL)isToday;

        #pragma mark - Date Components With Calendar

        - (NSInteger)eraWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)yearWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)monthWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)dayWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)hourWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)minuteWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)secondWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)weekdayWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)weekdayOrdinalWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)quarterWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)weekOfMonthWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)weekOfYearWithCalendar:(NSCalendar *)calendar;
        - (NSInteger)yearForWeekOfYearWithCalendar:(NSCalendar *)calendar;

        #pragma mark - Date Editing
        #pragma mark Date By Adding
        - (NSDate *)dateByAddingYears:(NSInteger)years;
        - (NSDate *)dateByAddingMonths:(NSInteger)months;
        - (NSDate *)dateByAddingWeeks:(NSInteger)weeks;
        - (NSDate *)dateByAddingDays:(NSInteger)days;
        - (NSDate *)dateByAddingHours:(NSInteger)hours;
        - (NSDate *)dateByAddingMinutes:(NSInteger)minutes;
        - (NSDate *)dateByAddingSeconds:(NSInteger)seconds;
        #pragma mark Date By Subtracting
        - (NSDate *)dateBySubtractingYears:(NSInteger)years;
        - (NSDate *)dateBySubtractingMonths:(NSInteger)months;
        - (NSDate *)dateBySubtractingWeeks:(NSInteger)weeks;
        - (NSDate *)dateBySubtractingDays:(NSInteger)days;
        - (NSDate *)dateBySubtractingHours:(NSInteger)hours;
        - (NSDate *)dateBySubtractingMinutes:(NSInteger)minutes;
        - (NSDate *)dateBySubtractingSeconds:(NSInteger)seconds;

        #pragma mark - Date Comparison
        #pragma mark Time From
        -(NSInteger)yearsFrom:(NSDate *)date;
        -(NSInteger)monthsFrom:(NSDate *)date;
        -(NSInteger)weeksFrom:(NSDate *)date;
        -(NSInteger)daysFrom:(NSDate *)date;
        -(double)hoursFrom:(NSDate *)date;
        -(double)minutesFrom:(NSDate *)date;
        -(double)secondsFrom:(NSDate *)date;
        #pragma mark Time From With Calendar
        -(NSInteger)yearsFrom:(NSDate *)date calendar:(NSCalendar *)calendar;
        -(NSInteger)monthsFrom:(NSDate *)date calendar:(NSCalendar *)calendar;
        -(NSInteger)weeksFrom:(NSDate *)date calendar:(NSCalendar *)calendar;
        -(NSInteger)daysFrom:(NSDate *)date calendar:(NSCalendar *)calendar;

        #pragma mark Time Until
        -(NSInteger)yearsUntil;
        -(NSInteger)monthsUntil;
        -(NSInteger)weeksUntil;
        -(NSInteger)daysUntil;
        -(double)hoursUntil;
        -(double)minutesUntil;
        -(double)secondsUntil;
        #pragma mark Time Ago
        -(NSInteger)yearsAgo;
        -(NSInteger)monthsAgo;
        -(NSInteger)weeksAgo;
        -(NSInteger)daysAgo;
        -(double)hoursAgo;
        -(double)minutesAgo;
        -(double)secondsAgo;
        #pragma mark Earlier Than
        -(NSInteger)yearsEarlierThan:(NSDate *)date;
        -(NSInteger)monthsEarlierThan:(NSDate *)date;
        -(NSInteger)weeksEarlierThan:(NSDate *)date;
        -(NSInteger)daysEarlierThan:(NSDate *)date;
        -(double)hoursEarlierThan:(NSDate *)date;
        -(double)minutesEarlierThan:(NSDate *)date;
        -(double)secondsEarlierThan:(NSDate *)date;
        #pragma mark Later Than
        -(NSInteger)yearsLaterThan:(NSDate *)date;
        -(NSInteger)monthsLaterThan:(NSDate *)date;
        -(NSInteger)weeksLaterThan:(NSDate *)date;
        -(NSInteger)daysLaterThan:(NSDate *)date;
        -(double)hoursLaterThan:(NSDate *)date;
        -(double)minutesLaterThan:(NSDate *)date;
        -(double)secondsLaterThan:(NSDate *)date;
        #pragma mark Comparators
        -(BOOL)isEarlierThan:(NSDate *)date;
        -(BOOL)isLaterThan:(NSDate *)date;
        -(BOOL)isEarlierThanOrEqualTo:(NSDate *)date;
        -(BOOL)isLaterThanOrEqualTo:(NSDate *)date;

        #pragma mark - Formatted Dates
        #pragma mark Formatted With Style
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style;
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style timeZone:(NSTimeZone *)timeZone;
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style locale:(NSLocale *)locale;
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style timeZone:(NSTimeZone *)timeZone locale:(NSLocale *)locale;
        #pragma mark Formatted With Format
        -(NSString *)formattedDateWithFormat:(NSString *)format;
        -(NSString *)formattedDateWithFormat:(NSString *)format timeZone:(NSTimeZone *)timeZone;
        -(NSString *)formattedDateWithFormat:(NSString *)format locale:(NSLocale *)locale;
        -(NSString *)formattedDateWithFormat:(NSString *)format timeZone:(NSTimeZone *)timeZone locale:(NSLocale *)locale;

        #pragma mark - Helpers
        +(NSString *)defaultCalendarIdentifier;
        + (void)setDefaultCalendarIdentifier:(NSString *)identifier;
        @end

2. NSDate+DateTools.h
        // Copyright (C) 2014 by Matthew York
        //
        // Permission is hereby granted, free of charge, to any
        // person obtaining a copy of this software and
        // associated documentation files (the "Software"), to
        // deal in the Software without restriction, including
        // without limitation the rights to use, copy, modify, merge,
        // publish, distribute, sublicense, and/or sell copies of the
        // Software, and to permit persons to whom the Software is
        // furnished to do so, subject to the following conditions:
        //
        // The above copyright notice and this permission notice shall
        // be included in all copies or substantial portions of the Software.
        //
        // THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
        // EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
        // OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
        // NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS
        // BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
        // ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
        // CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

        #import "NSDate+DateTools.h"

        typedef NS_ENUM(NSUInteger, DTDateComponent){
            DTDateComponentEra,
            DTDateComponentYear,
            DTDateComponentMonth,
            DTDateComponentDay,
            DTDateComponentHour,
            DTDateComponentMinute,
            DTDateComponentSecond,
            DTDateComponentWeekday,
            DTDateComponentWeekdayOrdinal,
            DTDateComponentQuarter,
            DTDateComponentWeekOfMonth,
            DTDateComponentWeekOfYear,
            DTDateComponentYearForWeekOfYear,
            DTDateComponentDayOfYear
        };

        static const unsigned int allCalendarUnitFlags = NSYearCalendarUnit | NSQuarterCalendarUnit | NSMonthCalendarUnit | NSWeekOfYearCalendarUnit | NSWeekOfMonthCalendarUnit | NSDayCalendarUnit | NSHourCalendarUnit | NSMinuteCalendarUnit | NSSecondCalendarUnit | NSEraCalendarUnit | NSWeekdayCalendarUnit | NSWeekdayOrdinalCalendarUnit | NSWeekCalendarUnit;

        static NSString *defaultCalendarIdentifier = nil;
        static NSCalendar *implicitCalendar = nil;

        @implementation NSDate (DateTools)

        + (void)load {
            [self setDefaultCalendarIdentifier:NSGregorianCalendar];
        }

        #pragma mark - Time Ago

        /**
         *  Takes in a date and returns a string with the most convenient unit of time representing
         *  how far in the past that date is from now.
         *
         *  @param NSDate - Date to be measured from now
         *
         *  @return NSString - Formatted return string
         */
        + (NSString*)timeAgoSinceDate:(NSDate*)date{
            return [date timeAgoSinceDate:[NSDate date]];
        }

        /**
         *  Takes in a date and returns a shortened string with the most convenient unit of time representing
         *  how far in the past that date is from now.
         *
         *  @param NSDate - Date to be measured from now
         *
         *  @return NSString - Formatted return string
         */
        + (NSString*)shortTimeAgoSinceDate:(NSDate*)date{
            return [date shortTimeAgoSinceDate:[NSDate date]];
        }

        /**
         *  Returns a string with the most convenient unit of time representing
         *  how far in the past that date is from now.
         *
         *  @return NSString - Formatted return string
         */
        - (NSString*)timeAgoSinceNow{
            return [self timeAgoSinceDate:[NSDate date]];
        }

        /**
         *  Returns a shortened string with the most convenient unit of time representing
         *  how far in the past that date is from now.
         *
         *  @return NSString - Formatted return string
         */
        - (NSString *)shortTimeAgoSinceNow{
            return [self shortTimeAgoSinceDate:[NSDate date]];
        }

        - (NSString *)timeAgoSinceDate:(NSDate *)date{
            return [self timeAgoSinceDate:date numericDates:NO];
        }

        - (NSString *)timeAgoSinceDate:(NSDate *)date numericDates:(BOOL)useNumericDates{

            NSCalendar *calendar = [NSCalendar currentCalendar];
            NSUInteger unitFlags = NSMinuteCalendarUnit | NSHourCalendarUnit | NSDayCalendarUnit | NSWeekCalendarUnit | NSMonthCalendarUnit | NSYearCalendarUnit | NSSecondCalendarUnit;
            NSDate *earliest = [self earlierDate:date];
            NSDate *latest = (earliest == self) ? date : self;
            NSDateComponents *components = [calendar components:unitFlags fromDate:earliest toDate:latest options:0];

            //Not Yet Implemented/Optional
            //The following strings are present in the translation files but lack logic as of 2014.04.05
            //@"Today", @"This week", @"This month", @"This year"
            //and @"This morning", @"This afternoon"

            if (components.year >= 2) {
                return  [self logicLocalizedStringFromFormat:@"%%d %@years ago" withValue:components.year];
            }
            else if (components.year >= 1) {

                if (useNumericDates) {
                    return DateToolsLocalizedStrings(@"1 year ago");
                }

                return DateToolsLocalizedStrings(@"Last year");
            }
            else if (components.month >= 2) {
                return [self logicLocalizedStringFromFormat:@"%%d %@months ago" withValue:components.month];
            }
            else if (components.month >= 1) {

                if (useNumericDates) {
                    return DateToolsLocalizedStrings(@"1 month ago");
                }

                return DateToolsLocalizedStrings(@"Last month");
            }
            else if (components.weekOfMonth >= 2) {
                return [self logicLocalizedStringFromFormat:@"%%d %@weeks ago" withValue:components.weekOfMonth];
            }
            else if (components.weekOfMonth >= 1) {

                if (useNumericDates) {
                    return DateToolsLocalizedStrings(@"1 week ago");
                }

                return DateToolsLocalizedStrings(@"Last week");
            }
            else if (components.day >= 2) {
                return [self logicLocalizedStringFromFormat:@"%%d %@days ago" withValue:components.day];
            }
            else if (components.day >= 1) {

                if (useNumericDates) {
                    return DateToolsLocalizedStrings(@"1 day ago");
                }

                return DateToolsLocalizedStrings(@"Yesterday");
            }
            else if (components.hour >= 2) {
                return [self logicLocalizedStringFromFormat:@"%%d %@hours ago" withValue:components.hour];
            }
            else if (components.hour >= 1) {
                return DateToolsLocalizedStrings(@"An hour ago");
            }
            else if (components.minute >= 2) {
                return [self logicLocalizedStringFromFormat:@"%%d %@minutes ago" withValue:components.minute];
            }
            else if (components.minute >= 1) {
                return DateToolsLocalizedStrings(@"A minute ago");
            }
            else if (components.second >= 3) {
                return [self logicLocalizedStringFromFormat:@"%%d %@seconds ago" withValue:components.second];
            }
            else {
                return DateToolsLocalizedStrings(@"Just now");
            }

        }

        - (NSString *)shortTimeAgoSinceDate:(NSDate *)date{

            //If shortened formatting is requested, drop the "ago" part of the time ago
            //use abbreviated unit names

            NSCalendar *calendar = [NSCalendar currentCalendar];
            NSUInteger unitFlags = NSMinuteCalendarUnit | NSHourCalendarUnit | NSDayCalendarUnit | NSWeekCalendarUnit | NSMonthCalendarUnit | NSYearCalendarUnit | NSSecondCalendarUnit;
            NSDate *earliest = [self earlierDate:date];
            NSDate *latest = (earliest == self) ? date : self;
            NSDateComponents *components = [calendar components:unitFlags fromDate:earliest toDate:latest options:0];

            if (components.year >= 1) {
                return  [self logicLocalizedStringFromFormat:@"%%d%@y" withValue:components.year];
            }
            else if (components.month >= 1) {
                return [self logicLocalizedStringFromFormat:@"%%d%@M" withValue:components.month];
            }
            else if (components.weekOfMonth >= 1) {
                return [self logicLocalizedStringFromFormat:@"%%d%@w" withValue:components.weekOfMonth];
            }
            else if (components.day >= 1) {
                return [self logicLocalizedStringFromFormat:@"%%d%@d" withValue:components.day];
            }
            else if (components.hour >= 1) {
                return [self logicLocalizedStringFromFormat:@"%%d%@h" withValue:components.hour];
            }
            else if (components.minute >= 1) {
                return [self logicLocalizedStringFromFormat:@"%%d%@m" withValue:components.minute];
            }
            else if (components.second >= 3) {
                return [self logicLocalizedStringFromFormat:@"%%d%@s" withValue:components.second];
            }
            else {
                return [self logicLocalizedStringFromFormat:@"%%d%@s" withValue:components.second];
                //return DateToolsLocalizedStrings(@"Now"); //string not yet translated 2014.04.05
            }

        }

        - (NSString *) logicLocalizedStringFromFormat:(NSString *)format withValue:(NSInteger)value{
            NSString * localeFormat = [NSString stringWithFormat:format, [self getLocaleFormatUnderscoresWithValue:value]];
            return [NSString stringWithFormat:DateToolsLocalizedStrings(localeFormat), value];
        }

        - (NSString *)getLocaleFormatUnderscoresWithValue:(double)value{
            NSString *localeCode = [[NSLocale preferredLanguages] objectAtIndex:0];

            // Russian (ru)
            if([localeCode isEqual:@"ru"]) {
                int XY = (int)floor(value) % 100;
                int Y = (int)floor(value) % 10;

                if(Y == 0 || Y > 4 || (XY > 10 && XY < 15)) {
                    return @"";
                }

                if(Y > 1 && Y < 5 && (XY < 10 || XY > 20))  {
                    return @"_";
                }

                if(Y == 1 && XY != 11) {
                    return @"__";
                }
            }

            // Add more languages here, which are have specific translation rules...

            return @"";
        }

        #pragma mark - Date Components Without Calendar
        /**
         *  Returns the era of the receiver. (0 for BC, 1 for AD for Gregorian)
         *
         *  @return NSInteger
         */
        - (NSInteger)era{
            return [self componentForDate:self type:DTDateComponentEra calendar:nil];
        }

        /**
         *  Returns the year of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)year{
            return [self componentForDate:self type:DTDateComponentYear calendar:nil];
        }

        /**
         *  Returns the month of the year of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)month{
            return [self componentForDate:self type:DTDateComponentMonth calendar:nil];
        }

        /**
         *  Returns the day of the month of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)day{
            return [self componentForDate:self type:DTDateComponentDay calendar:nil];
        }

        /**
         *  Returns the hour of the day of the receiver. (0-24)
         *
         *  @return NSInteger
         */
        - (NSInteger)hour{
            return [self componentForDate:self type:DTDateComponentHour calendar:nil];
        }

        /**
         *  Returns the minute of the receiver. (0-59)
         *
         *  @return NSInteger
         */
        - (NSInteger)minute{
            return [self componentForDate:self type:DTDateComponentMinute calendar:nil];
        }

        /**
         *  Returns the second of the receiver. (0-59)
         *
         *  @return NSInteger
         */
        - (NSInteger)second{
            return [self componentForDate:self type:DTDateComponentSecond calendar:nil];
        }

        /**
         *  Returns the day of the week of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)weekday{
            return [self componentForDate:self type:DTDateComponentWeekday calendar:nil];
        }

        /**
         *  Returns the ordinal for the day of the week of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)weekdayOrdinal{
            return [self componentForDate:self type:DTDateComponentWeekdayOrdinal calendar:nil];
        }

        /**
         *  Returns the quarter of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)quarter{
            return [self componentForDate:self type:DTDateComponentQuarter calendar:nil];
        }

        /**
         *  Returns the week of the month of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)weekOfMonth{
            return [self componentForDate:self type:DTDateComponentWeekOfMonth calendar:nil];
        }

        /**
         *  Returns the week of the year of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)weekOfYear{
            return [self componentForDate:self type:DTDateComponentWeekOfYear calendar:nil];
        }

        /**
         *  I honestly don't know much about this value...
         *
         *  @return NSInteger
         */
        - (NSInteger)yearForWeekOfYear{
            return [self componentForDate:self type:DTDateComponentYearForWeekOfYear calendar:nil];
        }

        /**
         *  Returns how many days are in the month of the receiver.
         *
         *  @return NSInteger
         */
        - (NSInteger)daysInMonth{
            NSCalendar *calendar = [NSCalendar currentCalendar];
            NSRange days = [calendar rangeOfUnit:NSDayCalendarUnit
                                          inUnit:NSMonthCalendarUnit
                                         forDate:self];
            return days.length;
        }

        /**
         *  Returns the day of the year of the receiver. (1-365 or 1-366 for leap year)
         *
         *  @return NSInteger
         */
        - (NSInteger)dayOfYear{
            return [self componentForDate:self type:DTDateComponentDayOfYear calendar:nil];
        }

        /**
         *  Returns how many days are in the year of the receiver.
         *
         *  @return NSInteger
         */
        -(NSInteger)daysInYear{
            if (self.isInLeapYear) {
                return 366;
            }

            return 365;
        }

        /**
         *  Returns whether the receiver falls in a leap year.
         *
         *  @return NSInteger
         */
        -(BOOL)isInLeapYear{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *dateComponents = [calendar components:allCalendarUnitFlags fromDate:self];

            if (dateComponents.year%400 == 0){
                return YES;
            }
            else if (dateComponents.year%100 == 0){
                return NO;
            }
            else if (dateComponents.year%4 == 0){
                return YES;
            }

            return NO;
        }

        - (BOOL)isToday {
        	NSCalendar *cal = [NSCalendar currentCalendar];
        	NSDateComponents *components = [cal components:(NSEraCalendarUnit|NSYearCalendarUnit|NSMonthCalendarUnit|NSDayCalendarUnit) fromDate:[NSDate date]];
        	NSDate *today = [cal dateFromComponents:components];
        	components = [cal components:(NSEraCalendarUnit|NSYearCalendarUnit|NSMonthCalendarUnit|NSDayCalendarUnit) fromDate:self];
        	NSDate *otherDate = [cal dateFromComponents:components];

        	return [today isEqualToDate:otherDate];
        }

        #pragma mark - Date Components With Calendar
        /**
         *  Returns the era of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the era (0 for BC, 1 for AD for Gregorian)
         */
        - (NSInteger)eraWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentEra calendar:calendar];
        }

        /**
         *  Returns the year of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the year as an integer
         */
        - (NSInteger)yearWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentYear calendar:calendar];
        }

        /**
         *  Returns the month of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the month as an integer
         */
        - (NSInteger)monthWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentMonth calendar:calendar];
        }

        /**
         *  Returns the day of the month of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the day of the month as an integer
         */
        - (NSInteger)dayWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentDay calendar:calendar];
        }

        /**
         *  Returns the hour of the day of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the hour of the day as an integer
         */
        - (NSInteger)hourWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentHour calendar:calendar];
        }

        /**
         *  Returns the minute of the hour of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the minute of the hour as an integer
         */
        - (NSInteger)minuteWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentMinute calendar:calendar];
        }

        /**
         *  Returns the second of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the second as an integer
         */
        - (NSInteger)secondWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentSecond calendar:calendar];
        }

        /**
         *  Returns the weekday of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the weekday as an integer
         */
        - (NSInteger)weekdayWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentWeekday calendar:calendar];
        }

        /**
         *  Returns the weekday ordinal of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the weekday ordinal as an integer
         */
        - (NSInteger)weekdayOrdinalWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentWeekdayOrdinal calendar:calendar];
        }

        /**
         *  Returns the quarter of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the quarter as an integer
         */
        - (NSInteger)quarterWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentQuarter calendar:calendar];
        }

        /**
         *  Returns the week of the month of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the week of the month as an integer
         */
        - (NSInteger)weekOfMonthWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentWeekOfMonth calendar:calendar];
        }

        /**
         *  Returns the week of the year of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the week of the year as an integer
         */
        - (NSInteger)weekOfYearWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentWeekOfYear calendar:calendar];
        }

        /**
         *  Returns the year for week of the year (???) of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the year for week of the year as an integer
         */
        - (NSInteger)yearForWeekOfYearWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentYearForWeekOfYear calendar:calendar];
        }

        /**
         *  Returns the day of the year of the receiver from a given calendar
         *
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - represents the day of the year as an integer
         */
        - (NSInteger)dayOfYearWithCalendar:(NSCalendar *)calendar{
            return [self componentForDate:self type:DTDateComponentDayOfYear calendar:calendar];
        }

        /**
         *  Takes in a date, calendar and desired date component and returns the desired NSInteger
         *  representation for that component
         *
         *  @param date      NSDate - The date to be be mined for a desired component
         *  @param component DTDateComponent - The desired component (i.e. year, day, week, etc)
         *  @param calendar  NSCalendar - The calendar to be used in the processing (Defaults to Gregorian)
         *
         *  @return NSInteger
         */
        -(NSInteger)componentForDate:(NSDate *)date type:(DTDateComponent)component calendar:(NSCalendar *)calendar{
            if (!calendar) {
                calendar = [[self class] implicitCalendar];
            }

            unsigned int unitFlags = 0;

            if (component == DTDateComponentYearForWeekOfYear) {
               unitFlags = NSYearCalendarUnit | NSQuarterCalendarUnit | NSMonthCalendarUnit | NSWeekOfYearCalendarUnit | NSWeekOfMonthCalendarUnit | NSDayCalendarUnit | NSHourCalendarUnit | NSMinuteCalendarUnit | NSSecondCalendarUnit | NSEraCalendarUnit | NSWeekdayCalendarUnit | NSWeekdayOrdinalCalendarUnit | NSWeekCalendarUnit | NSYearForWeekOfYearCalendarUnit;
            }
            else {
                unitFlags = allCalendarUnitFlags;
            }

            NSDateComponents *dateComponents = [calendar components:unitFlags fromDate:date];

            switch (component) {
                case DTDateComponentEra:
                    return [dateComponents era];
                case DTDateComponentYear:
                    return [dateComponents year];
                case DTDateComponentMonth:
                    return [dateComponents month];
                case DTDateComponentDay:
                    return [dateComponents day];
                case DTDateComponentHour:
                    return [dateComponents hour];
                case DTDateComponentMinute:
                    return [dateComponents minute];
                case DTDateComponentSecond:
                    return [dateComponents second];
                case DTDateComponentWeekday:
                    return [dateComponents weekday];
                case DTDateComponentWeekdayOrdinal:
                    return [dateComponents weekdayOrdinal];
                case DTDateComponentQuarter:
                    return [dateComponents quarter];
                case DTDateComponentWeekOfMonth:
                    return [dateComponents weekOfMonth];
                case DTDateComponentWeekOfYear:
                    return [dateComponents weekOfYear];
                case DTDateComponentYearForWeekOfYear:
                    return [dateComponents yearForWeekOfYear];
                case DTDateComponentDayOfYear:
                    return [calendar ordinalityOfUnit:NSDayCalendarUnit inUnit:NSYearCalendarUnit forDate:date];
                default:
                    break;
            }

            return 0;
        }

        #pragma mark - Date Editing
        #pragma mark Date By Adding
        /**
         *  Returns a date representing the receivers date shifted later by the provided number of years.
         *
         *  @param years NSInteger - Number of years to add
         *
         *  @return NSDate - Date modified by the number of desired years
         */
        - (NSDate *)dateByAddingYears:(NSInteger)years{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setYear:years];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted later by the provided number of months.
         *
         *  @param years NSInteger - Number of months to add
         *
         *  @return NSDate - Date modified by the number of desired months
         */
        - (NSDate *)dateByAddingMonths:(NSInteger)months{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setMonth:months];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted later by the provided number of weeks.
         *
         *  @param years NSInteger - Number of weeks to add
         *
         *  @return NSDate - Date modified by the number of desired weeks
         */
        - (NSDate *)dateByAddingWeeks:(NSInteger)weeks{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setWeekOfMonth:weeks];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted later by the provided number of days.
         *
         *  @param years NSInteger - Number of days to add
         *
         *  @return NSDate - Date modified by the number of desired days
         */
        - (NSDate *)dateByAddingDays:(NSInteger)days{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setDay:days];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted later by the provided number of hours.
         *
         *  @param years NSInteger - Number of hours to add
         *
         *  @return NSDate - Date modified by the number of desired hours
         */
        - (NSDate *)dateByAddingHours:(NSInteger)hours{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setHour:hours];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted later by the provided number of minutes.
         *
         *  @param years NSInteger - Number of minutes to add
         *
         *  @return NSDate - Date modified by the number of desired minutes
         */
        - (NSDate *)dateByAddingMinutes:(NSInteger)minutes{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setMinute:minutes];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted later by the provided number of seconds.
         *
         *  @param years NSInteger - Number of seconds to add
         *
         *  @return NSDate - Date modified by the number of desired seconds
         */
        - (NSDate *)dateByAddingSeconds:(NSInteger)seconds{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setSecond:seconds];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        #pragma mark Date By Subtracting
        /**
         *  Returns a date representing the receivers date shifted earlier by the provided number of years.
         *
         *  @param years NSInteger - Number of years to subtract
         *
         *  @return NSDate - Date modified by the number of desired years
         */
        - (NSDate *)dateBySubtractingYears:(NSInteger)years{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setYear:-1*years];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted earlier by the provided number of months.
         *
         *  @param years NSInteger - Number of months to subtract
         *
         *  @return NSDate - Date modified by the number of desired months
         */
        - (NSDate *)dateBySubtractingMonths:(NSInteger)months{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setMonth:-1*months];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted earlier by the provided number of weeks.
         *
         *  @param years NSInteger - Number of weeks to subtract
         *
         *  @return NSDate - Date modified by the number of desired weeks
         */
        - (NSDate *)dateBySubtractingWeeks:(NSInteger)weeks{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setWeekOfMonth:-1*weeks];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted earlier by the provided number of days.
         *
         *  @param years NSInteger - Number of days to subtract
         *
         *  @return NSDate - Date modified by the number of desired days
         */
        - (NSDate *)dateBySubtractingDays:(NSInteger)days{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setDay:-1*days];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted earlier by the provided number of hours.
         *
         *  @param years NSInteger - Number of hours to subtract
         *
         *  @return NSDate - Date modified by the number of desired hours
         */
        - (NSDate *)dateBySubtractingHours:(NSInteger)hours{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setHour:-1*hours];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted earlier by the provided number of minutes.
         *
         *  @param years NSInteger - Number of minutes to subtract
         *
         *  @return NSDate - Date modified by the number of desired minutes
         */
        - (NSDate *)dateBySubtractingMinutes:(NSInteger)minutes{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setMinute:-1*minutes];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        /**
         *  Returns a date representing the receivers date shifted earlier by the provided number of seconds.
         *
         *  @param years NSInteger - Number of seconds to subtract
         *
         *  @return NSDate - Date modified by the number of desired seconds
         */
        - (NSDate *)dateBySubtractingSeconds:(NSInteger)seconds{
            NSCalendar *calendar = [[self class] implicitCalendar];
            NSDateComponents *components = [[NSDateComponents alloc] init];
            [components setSecond:-1*seconds];

            return [calendar dateByAddingComponents:components toDate:self options:0];
        }

        #pragma mark - Date Comparison
        #pragma mark Time From
        /**
         *  Returns an NSInteger representing the amount of time in years between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *  Uses the default Gregorian calendar
         *
         *  @param date NSDate - The provided date for comparison
         *
         *  @return NSInteger - The NSInteger representation of the years between receiver and provided date
         */
        -(NSInteger)yearsFrom:(NSDate *)date{
            return [self yearsFrom:date calendar:nil];
        }

        /**
         *  Returns an NSInteger representing the amount of time in months between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *  Uses the default Gregorian calendar
         *
         *  @param date NSDate - The provided date for comparison
         *
         *  @return NSInteger - The NSInteger representation of the years between receiver and provided date
         */
        -(NSInteger)monthsFrom:(NSDate *)date{
            return [self monthsFrom:date calendar:nil];
        }

        /**
         *  Returns an NSInteger representing the amount of time in weeks between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *  Uses the default Gregorian calendar
         *
         *  @param date NSDate - The provided date for comparison
         *
         *  @return NSInteger - The double representation of the weeks between receiver and provided date
         */
        -(NSInteger)weeksFrom:(NSDate *)date{
            return [self weeksFrom:date calendar:nil];
        }

        /**
         *  Returns an NSInteger representing the amount of time in days between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *  Uses the default Gregorian calendar
         *
         *  @param date NSDate - The provided date for comparison
         *
         *  @return NSInteger - The double representation of the days between receiver and provided date
         */
        -(NSInteger)daysFrom:(NSDate *)date{
            return [self daysFrom:date calendar:nil];
        }

        /**
         *  Returns an NSInteger representing the amount of time in hours between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *
         *  @param date NSDate - The provided date for comparison
         *
         *  @return double - The double representation of the hours between receiver and provided date
         */
        -(double)hoursFrom:(NSDate *)date{
            return ([self timeIntervalSinceDate:date])/SECONDS_IN_HOUR;
        }

        /**
         *  Returns an NSInteger representing the amount of time in minutes between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *
         *  @param date NSDate - The provided date for comparison
         *
         *  @return double - The double representation of the minutes between receiver and provided date
         */
        -(double)minutesFrom:(NSDate *)date{
            return ([self timeIntervalSinceDate:date])/SECONDS_IN_MINUTE;
        }

        /**
         *  Returns an NSInteger representing the amount of time in seconds between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *
         *  @param date NSDate - The provided date for comparison
         *
         *  @return double - The double representation of the seconds between receiver and provided date
         */
        -(double)secondsFrom:(NSDate *)date{
            return [self timeIntervalSinceDate:date];
        }

        #pragma mark Time From With Calendar
        /**
         *  Returns an NSInteger representing the amount of time in years between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *
         *  @param date     NSDate - The provided date for comparison
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - The double representation of the years between receiver and provided date
         */
        -(NSInteger)yearsFrom:(NSDate *)date calendar:(NSCalendar *)calendar{
            if (!calendar) {
                calendar = [[self class] implicitCalendar];
            }

            NSDate *earliest = [self earlierDate:date];
            NSDate *latest = (earliest == self) ? date : self;
            NSInteger multiplier = (earliest == self) ? -1 : 1;
            NSDateComponents *components = [calendar components:NSYearCalendarUnit fromDate:earliest toDate:latest options:0];
            return multiplier*components.year;
        }

        /**
         *  Returns an NSInteger representing the amount of time in months between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *
         *  @param date     NSDate - The provided date for comparison
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - The double representation of the months between receiver and provided date
         */
        -(NSInteger)monthsFrom:(NSDate *)date calendar:(NSCalendar *)calendar{
            if (!calendar) {
                calendar = [[self class] implicitCalendar];
            }

            NSDate *earliest = [self earlierDate:date];
            NSDate *latest = (earliest == self) ? date : self;
            NSInteger multiplier = (earliest == self) ? -1 : 1;
            NSDateComponents *components = [calendar components:allCalendarUnitFlags fromDate:earliest toDate:latest options:0];
            return multiplier*(components.month + 12*components.year);
        }

        /**
         *  Returns an NSInteger representing the amount of time in weeks between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *
         *  @param date     NSDate - The provided date for comparison
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - The double representation of the weeks between receiver and provided date
         */
        -(NSInteger)weeksFrom:(NSDate *)date calendar:(NSCalendar *)calendar{
            if (!calendar) {
                calendar = [[self class] implicitCalendar];
            }

            NSDate *earliest = [self earlierDate:date];
            NSDate *latest = (earliest == self) ? date : self;
            NSInteger multiplier = (earliest == self) ? -1 : 1;
            NSDateComponents *components = [calendar components:NSWeekCalendarUnit fromDate:earliest toDate:latest options:0];
            return multiplier*components.weekOfMonth;
        }

        /**
         *  Returns an NSInteger representing the amount of time in days between the receiver and the provided date.
         *  If the receiver is earlier than the provided date, the returned value will be negative.
         *
         *  @param date     NSDate - The provided date for comparison
         *  @param calendar NSCalendar - The calendar to be used in the calculation
         *
         *  @return NSInteger - The double representation of the days between receiver and provided date
         */
        -(NSInteger)daysFrom:(NSDate *)date calendar:(NSCalendar *)calendar{
            if (!calendar) {
                calendar = [[self class] implicitCalendar];
            }

            NSDate *earliest = [self earlierDate:date];
            NSDate *latest = (earliest == self) ? date : self;
            NSInteger multiplier = (earliest == self) ? -1 : 1;
            NSDateComponents *components = [calendar components:NSDayCalendarUnit fromDate:earliest toDate:latest options:0];
            return multiplier*components.day;
        }

        #pragma mark Time Until
        /**
         *  Returns the number of years until the receiver's date. Returns 0 if the receiver is the same or earlier than now.
         *
         *  @return NSInteger representiation of years
         */
        -(NSInteger)yearsUntil{
            return [self yearsLaterThan:[NSDate date]];
        }

        /**
         *  Returns the number of months until the receiver's date. Returns 0 if the receiver is the same or earlier than now.
         *
         *  @return NSInteger representiation of months
         */
        -(NSInteger)monthsUntil{
            return [self monthsLaterThan:[NSDate date]];
        }

        /**
         *  Returns the number of weeks until the receiver's date. Returns 0 if the receiver is the same or earlier than now.
         *
         *  @return NSInteger representiation of weeks
         */
        -(NSInteger)weeksUntil{
            return [self weeksLaterThan:[NSDate date]];
        }

        /**
         *  Returns the number of days until the receiver's date. Returns 0 if the receiver is the same or earlier than now.
         *
         *  @return NSInteger representiation of days
         */
        -(NSInteger)daysUntil{
            return [self daysLaterThan:[NSDate date]];
        }

        /**
         *  Returns the number of hours until the receiver's date. Returns 0 if the receiver is the same or earlier than now.
         *
         *  @return double representiation of hours
         */
        -(double)hoursUntil{
            return [self hoursLaterThan:[NSDate date]];
        }

        /**
         *  Returns the number of minutes until the receiver's date. Returns 0 if the receiver is the same or earlier than now.
         *
         *  @return double representiation of minutes
         */
        -(double)minutesUntil{
            return [self minutesLaterThan:[NSDate date]];
        }

        /**
         *  Returns the number of seconds until the receiver's date. Returns 0 if the receiver is the same or earlier than now.
         *
         *  @return double representiation of seconds
         */
        -(double)secondsUntil{
            return [self secondsLaterThan:[NSDate date]];
        }

        #pragma mark Time Ago
        /**
         *  Returns the number of years the receiver's date is earlier than now. Returns 0 if the receiver is the same or later than now.
         *
         *  @return NSInteger representiation of years
         */
        -(NSInteger)yearsAgo{
            return [self yearsEarlierThan:[NSDate date]];
        }

        /**
         *  Returns the number of months the receiver's date is earlier than now. Returns 0 if the receiver is the same or later than now.
         *
         *  @return NSInteger representiation of months
         */
        -(NSInteger)monthsAgo{
            return [self monthsEarlierThan:[NSDate date]];
        }

        /**
         *  Returns the number of weeks the receiver's date is earlier than now. Returns 0 if the receiver is the same or later than now.
         *
         *  @return NSInteger representiation of weeks
         */
        -(NSInteger)weeksAgo{
            return [self weeksEarlierThan:[NSDate date]];
        }

        /**
         *  Returns the number of days the receiver's date is earlier than now. Returns 0 if the receiver is the same or later than now.
         *
         *  @return NSInteger representiation of days
         */
        -(NSInteger)daysAgo{
            return [self daysEarlierThan:[NSDate date]];
        }

        /**
         *  Returns the number of hours the receiver's date is earlier than now. Returns 0 if the receiver is the same or later than now.
         *
         *  @return double representiation of hours
         */
        -(double)hoursAgo{
            return [self hoursEarlierThan:[NSDate date]];
        }

        /**
         *  Returns the number of minutes the receiver's date is earlier than now. Returns 0 if the receiver is the same or later than now.
         *
         *  @return double representiation of minutes
         */
        -(double)minutesAgo{
            return [self minutesEarlierThan:[NSDate date]];
        }

        /**
         *  Returns the number of seconds the receiver's date is earlier than now. Returns 0 if the receiver is the same or later than now.
         *
         *  @return double representiation of seconds
         */
        -(double)secondsAgo{
            return [self secondsEarlierThan:[NSDate date]];
        }

        #pragma mark Earlier Than
        /**
         *  Returns the number of years the receiver's date is earlier than the provided comparison date.
         *  Returns 0 if the receiver's date is later than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of years
         */
        -(NSInteger)yearsEarlierThan:(NSDate *)date{
            return ABS(MIN([self yearsFrom:date], 0));
        }

        /**
         *  Returns the number of months the receiver's date is earlier than the provided comparison date.
         *  Returns 0 if the receiver's date is later than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of months
         */
        -(NSInteger)monthsEarlierThan:(NSDate *)date{
            return ABS(MIN([self monthsFrom:date], 0));
        }

        /**
         *  Returns the number of weeks the receiver's date is earlier than the provided comparison date.
         *  Returns 0 if the receiver's date is later than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of weeks
         */
        -(NSInteger)weeksEarlierThan:(NSDate *)date{
            return ABS(MIN([self weeksFrom:date], 0));
        }

        /**
         *  Returns the number of days the receiver's date is earlier than the provided comparison date.
         *  Returns 0 if the receiver's date is later than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of days
         */
        -(NSInteger)daysEarlierThan:(NSDate *)date{
            return ABS(MIN([self daysFrom:date], 0));
        }

        /**
         *  Returns the number of hours the receiver's date is earlier than the provided comparison date.
         *  Returns 0 if the receiver's date is later than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return double representing the number of hours
         */
        -(double)hoursEarlierThan:(NSDate *)date{
            return ABS(MIN([self hoursFrom:date], 0));
        }

        /**
         *  Returns the number of minutes the receiver's date is earlier than the provided comparison date.
         *  Returns 0 if the receiver's date is later than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return double representing the number of minutes
         */
        -(double)minutesEarlierThan:(NSDate *)date{
            return ABS(MIN([self minutesFrom:date], 0));
        }

        /**
         *  Returns the number of seconds the receiver's date is earlier than the provided comparison date.
         *  Returns 0 if the receiver's date is later than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return double representing the number of seconds
         */
        -(double)secondsEarlierThan:(NSDate *)date{
            return ABS(MIN([self secondsFrom:date], 0));
        }

        #pragma mark Later Than
        /**
         *  Returns the number of years the receiver's date is later than the provided comparison date.
         *  Returns 0 if the receiver's date is earlier than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of years
         */
        -(NSInteger)yearsLaterThan:(NSDate *)date{
            return MAX([self yearsFrom:date], 0);
        }

        /**
         *  Returns the number of months the receiver's date is later than the provided comparison date.
         *  Returns 0 if the receiver's date is earlier than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of months
         */
        -(NSInteger)monthsLaterThan:(NSDate *)date{
            return MAX([self monthsFrom:date], 0);
        }

        /**
         *  Returns the number of weeks the receiver's date is later than the provided comparison date.
         *  Returns 0 if the receiver's date is earlier than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of weeks
         */
        -(NSInteger)weeksLaterThan:(NSDate *)date{
            return MAX([self weeksFrom:date], 0);
        }

        /**
         *  Returns the number of days the receiver's date is later than the provided comparison date.
         *  Returns 0 if the receiver's date is earlier than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return NSInteger representing the number of days
         */
        -(NSInteger)daysLaterThan:(NSDate *)date{
            return MAX([self daysFrom:date], 0);
        }

        /**
         *  Returns the number of hours the receiver's date is later than the provided comparison date.
         *  Returns 0 if the receiver's date is earlier than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return double representing the number of hours
         */
        -(double)hoursLaterThan:(NSDate *)date{
            return MAX([self hoursFrom:date], 0);
        }

        /**
         *  Returns the number of minutes the receiver's date is later than the provided comparison date.
         *  Returns 0 if the receiver's date is earlier than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return double representing the number of minutes
         */
        -(double)minutesLaterThan:(NSDate *)date{
            return MAX([self minutesFrom:date], 0);
        }

        /**
         *  Returns the number of seconds the receiver's date is later than the provided comparison date.
         *  Returns 0 if the receiver's date is earlier than or equal to the provided comparison date.
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return double representing the number of seconds
         */
        -(double)secondsLaterThan:(NSDate *)date{
            return MAX([self secondsFrom:date], 0);
        }

        #pragma mark Comparators
        /**
         *  Returns a YES if receiver is earlier than provided comparison date, otherwise returns NO
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return BOOL representing comparison result
         */
        -(BOOL)isEarlierThan:(NSDate *)date{
            if (self.timeIntervalSince1970 < date.timeIntervalSince1970) {
                return YES;
            }
            return NO;
        }

        /**
         *  Returns a YES if receiver is later than provided comparison date, otherwise returns NO
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return BOOL representing comparison result
         */
        -(BOOL)isLaterThan:(NSDate *)date{
            if (self.timeIntervalSince1970 > date.timeIntervalSince1970) {
                return YES;
            }
            return NO;
        }

        /**
         *  Returns a YES if receiver is earlier than or equal to the provided comparison date, otherwise returns NO
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return BOOL representing comparison result
         */
        -(BOOL)isEarlierThanOrEqualTo:(NSDate *)date{
            if (self.timeIntervalSince1970 <= date.timeIntervalSince1970) {
                return YES;
            }
            return NO;
        }

        /**
         *  Returns a YES if receiver is later than or equal to provided comparison date, otherwise returns NO
         *
         *  @param date NSDate - Provided date for comparison
         *
         *  @return BOOL representing comparison result
         */
        -(BOOL)isLaterThanOrEqualTo:(NSDate *)date{
            if (self.timeIntervalSince1970 >= date.timeIntervalSince1970) {
                return YES;
            }
            return NO;
        }

        #pragma mark - Formatted Dates
        #pragma mark Formatted With Style
        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given style
         *
         *  @param style NSDateFormatterStyle - Desired date formatting style
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style{
            return [self formattedDateWithStyle:style timeZone:[NSTimeZone systemTimeZone] locale:[NSLocale autoupdatingCurrentLocale]];
        }

        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given style and time zone
         *
         *  @param style    NSDateFormatterStyle - Desired date formatting style
         *  @param timeZone NSTimeZone - Desired time zone
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style timeZone:(NSTimeZone *)timeZone{
            return [self formattedDateWithStyle:style timeZone:timeZone locale:[NSLocale autoupdatingCurrentLocale]];
        }

        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given style and locale
         *
         *  @param style  NSDateFormatterStyle - Desired date formatting style
         *  @param locale NSLocale - Desired locale
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style locale:(NSLocale *)locale{
            return [self formattedDateWithStyle:style timeZone:[NSTimeZone systemTimeZone] locale:locale];
        }

        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given style, time zone and locale
         *
         *  @param style    NSDateFormatterStyle - Desired date formatting style
         *  @param timeZone NSTimeZone - Desired time zone
         *  @param locale   NSLocale - Desired locale
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style timeZone:(NSTimeZone *)timeZone locale:(NSLocale *)locale{
            static NSDateFormatter *formatter = nil;
            static dispatch_once_t onceToken;
            dispatch_once(&onceToken, ^{
                formatter = [[NSDateFormatter alloc] init];
            });

            [formatter setDateStyle:style];
            [formatter setTimeZone:timeZone];
            [formatter setLocale:locale];
            return [formatter stringFromDate:self];
        }

        #pragma mark Formatted With Format
        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given date format
         *
         *  @param format NSString - String representing the desired date format
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithFormat:(NSString *)format{
            return [self formattedDateWithFormat:format timeZone:[NSTimeZone systemTimeZone] locale:[NSLocale autoupdatingCurrentLocale]];
        }

        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given date format and time zone
         *
         *  @param format   NSString - String representing the desired date format
         *  @param timeZone NSTimeZone - Desired time zone
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithFormat:(NSString *)format timeZone:(NSTimeZone *)timeZone{
            return [self formattedDateWithFormat:format timeZone:timeZone locale:[NSLocale autoupdatingCurrentLocale]];
        }

        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given date format and locale
         *
         *  @param format NSString - String representing the desired date format
         *  @param locale NSLocale - Desired locale
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithFormat:(NSString *)format locale:(NSLocale *)locale{
            return [self formattedDateWithFormat:format timeZone:[NSTimeZone systemTimeZone] locale:locale];
        }

        /**
         *  Convenience method that returns a formatted string representing the receiver's date formatted to a given date format, time zone and locale
         *
         *  @param format   NSString - String representing the desired date format
         *  @param timeZone NSTimeZone - Desired time zone
         *  @param locale   NSLocale - Desired locale
         *
         *  @return NSString representing the formatted date string
         */
        -(NSString *)formattedDateWithFormat:(NSString *)format timeZone:(NSTimeZone *)timeZone locale:(NSLocale *)locale{
            static NSDateFormatter *formatter = nil;
            static dispatch_once_t onceToken;
            dispatch_once(&onceToken, ^{
                formatter = [[NSDateFormatter alloc] init];
            });

            [formatter setDateFormat:format];
            [formatter setTimeZone:timeZone];
            [formatter setLocale:locale];
            return [formatter stringFromDate:self];
        }

        #pragma mark - Helpers
        /**
         *  Class method that returns whether the given year is a leap year for the Gregorian Calendar
         *  Returns YES if year is a leap year, otherwise returns NO
         *
         *  @param year NSInteger - Year to evaluate
         *
         *  @return BOOL evaluation of year
         */
        +(BOOL)isLeapYear:(NSInteger)year{
            if (year*400){
                return YES;
            }
            else if (year%100){
                return NO;
            }
            else if (year%4){
                return YES;
            }

            return NO;
        }

        /**
         *  Retrieves the default calendar identifier used for all non-calendar-specified operations
         *
         *  @return NSString - NSCalendarIdentifier
         */
        +(NSString *)defaultCalendarIdentifier {
            return defaultCalendarIdentifier;
        }

        /**
         *  Sets the default calendar identifier used for all non-calendar-specified operations
         *
         *  @param identifier NSString - NSCalendarIdentifier
         */
        + (void)setDefaultCalendarIdentifier:(NSString *)identifier {
            defaultCalendarIdentifier = [identifier copy];
            implicitCalendar = [[NSCalendar alloc] initWithCalendarIdentifier:defaultCalendarIdentifier ?: NSGregorianCalendar];
        }

        /**
         *  Retrieves a default NSCalendar instance, based on the value of defaultCalendarSetting
         *
         *  @return NSCalendar The current implicit calendar
         */
        + (NSCalendar *)implicitCalendar {
            return implicitCalendar;
        }

        @end

######3. 时间显示格式

| 格式 | 显示 |
| -- | -- |
| dd/MM/yyyy hh:mm a | 30/03/2015  4:00 PM |
| MMM dd,YYYY | Apr 11,2015 |
| EEE MMM dd HH:mm:ss Z yyyy | Thu Apr 09 14:37:22 +0800 2015 |

#####4. 时间工具方法

1. 从 NSString -> NSDate
        + (NSDate *)getDateFromString:(NSString *)pstrDate format:(NSString *)aFormat
        {
            if (pstrDate.length == 0)
            {
                return nil;
            }

            NSDateFormatter *df1 = [[NSDateFormatter alloc] init];
            [df1 setDateFormat:aFormat];
            NSDate *dtPostDate = [df1 dateFromString:pstrDate];
            return dtPostDate;
        }

### 效果图
（无）

### 备注
（无）

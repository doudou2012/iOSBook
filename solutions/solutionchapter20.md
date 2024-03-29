### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-03 | Alfred Jiang | - |

### 方案名称
iOS 应用测试Checklist以及思维导图

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
Checklist \ 测试 \ Test \ 思维导图

### 需求场景
1. 应用测试

### 参考链接
1. [GitHub](https://github.com/oisin/app-release-checklist/blob/master/checklist.md)

### 详细内容

#####一、 App Development Company Limited

*RELEASE AUTHORISATION FORM v1.0*

|  |  |
|:-------------------------------------|-------------------------------------:|
| App name                             |      *insert content*                |
| Version                              |      *insert content*                |
| Date of submission                   |      *insert content*           |

This form is to document the testing that has been done on each app
version before submitting to the App Store. For each item, indicate *Yes*
if the testing has been done, *Not Applicable* if the testing does not
apply (eg testing audio for an app that doesn’t play any), or *No* if the
testing has not been done for another reason.

--

######Internet Connectivity
Test all the data downloading sections of the app by trying them on the appropriate connection type. Consider graceful degradation and failure as well as success conditions.

| Connection | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Wifi | | | |
| Edge | | | |
| GPRS | | | |
| No Network | | | |
| Break in Network - use Charles | | | |
| Server unreachable - timeout | | | |
| Resumed connect - streaming only | | | - |

--

######Locale
Change device’s settings then load the app. Check that dates appear correctly, especially dates from external feeds or services.

| Locale | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| 12 and 24 hour clocks | | | |
| Regions: **fork and add regions for you** | | | |
| Languages: **fork and add languages for you** | | | |
| Timezones  | | | |
| Daylight Savings Time | | | - |

--

###### Devices
Run the application through navigations using different devices with different iOS versions and display formats.

| Device | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| iPhone / iPod touch running iOS 5.0 | | | |
| iPhone / iPod touch running iOS 5.1.1 | | | |
| iPhone / iPod touch running iOS 6.0 | | | |
| iPhone / iPod touch running iOS 6.1.3 | | | |
| iPhone / iPod touch running iOS 7.0 | | | |
| Retina iPhone display | | | |
| Non-retina iPhone display | | | |
| iPad 1 running iOS 5.0 | | | |
| iPad 1 running iOS 5.0 | | | |
| iPhone / iPod touch running iOS 5.1.1 | | | |
| iPad running iOS 6.0 | | | |
| iPad running iOS 6.1.3 | | | |
| iPad running iOS 7.0 | | | |
| Retina iPad display | | | |
| Non-retina iPad display | | | |
| iPad mini display | | | - |

--

###### Audio
If app plays audio, perform the following checks. For streaming audio, make sure the checks in the network section above have also been done.

| Audio | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Headphones/speaker routing  | | | |
| Dock connector audio out routing   | | | |
| iPod touch audio routing (consider model without speaker) | | | |
| Mute switch functionality (officially it mutes non-user-requested sounds) | | | |
| Audio pause on received phone call | | | |
| Background audio (if supported): playback and multitasking bar controls | | | |
| Start playing audio when another app is already playing | | | |
| Headphone remote for audio control | | | |
| Multitasking screen audio control | | | - |

--

######Video
Streaming video should have been checked in the network tests.

| Video | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| User cancels video before playback begins  | | | |
| User cancels video during playback  | | | |
| Video plays to the end  | | | |
| Video return from full screen  | | | |
| Dock connector video out  | | | |
| Video transition between inline and full screen  | | | - |

--

######Location

| Location | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| True GPS | | | |
| Wifi location | | | |
| Cell tower location | | | |
| Unable to find location | | | |
| No results returned (e.g. too far from any searchable points of interest)  | | | |
| Location services turned off | | | |
| Location services disabled for this app | | | - |

--

######Camera / Video
If app takes pictures or video clips, perform the following checks. For streaming video, make sure the checks in the network section above have also been done.

| Camera / Video | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Primary camera photo taken | | | |
| Primary camera video captured | | | |
| Secondary (user facing) camera taken | | | |
| Secondary (user facing) video captured | | | |
| Video recording paused on received phone call | | | - |

--

######Logging

| Logging | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Logging events to live server | | | |
| Logging errors (interact with other tests?) | | | - |

--

######User Interface
Test each major view in the app.

| Title | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Double height status bar (eg in call) | | | |
| Orientation change | | | |
| Upside-down orientation | | | |
| Orientation lock | | | |
| VoiceOver turned on | | | |
| Usable by a new user with Screen Curtain turned on | | | |
| Works with Accessibility Zoom turned on | | | - |

--

######Core Data

| Core Data | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Validation error in user input | | | |
| Validation error in web server input | | | |
| Test migrations with valid and invalid data files | | | |
| Rollback | | | - |

--

######Installation

| Installation | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Fresh install | | | |
| Upgrade from previous live version | | | |
| Upgrade from older live version | | | |
| Rollback | | | - |

--

######Text

| Title | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Shake to Undo | | | |
| Text selection (including disabled when appropriate) | | | |
| Copy / Paste | | | |
| Editing when keyboard is hidden | | | |
| Dictionary / Suggested Word hover | | | - |

--

######Third Party Services
All third party services should use production API key and the new app version should be registered in the respective dashboards

| Title | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Production analytics/tracking API key | | | |
| New app version tracking data available tracking in dashboard | | | |
| Production crash reporting API key | | | |
| Upload dSYM to crash reporting tool | | | |
| New app version available in crash reporting dashboard | | | |
| Push notification service API key | | | |
| New app version added to push service dashboard | | | |
| Production App ID for social services (Twitter, Facebook, Instagram, etc) | | | - |

--

######Misc

| Misc | N/A | NO | YES |
|:---|:---:|:---:|:---:|
| Bluetooth | | | |
| Motion | | | |
| Tested in Ad Hoc mode | | | |
| Version number upgraded | | | |
| Bundle identifier correct for release | | | - |

--

Sign-off: __________________________________________

Project role: _______________________________________


#####二、iOS Testing

	Hardware
		iPhone
			iPhone 3G
			iPhone 3GS
			iPhone 4 (retina)
			iPhone 4S (retina/dual core)
		iPod
			3rd generation (no camera)
		iPad
			iPad 1st gen
			iPad 2 (dual core)
			The new iPad (retina / dual core)
	UI
		Accessibility
		Half pixels
		Retina display
		Non-retina display
		Extended status bar resizing
		Portrait
		Landscape
		Smooth animations
	Functionality
		Location services
		Email
			Email configured
				Default to
				Default subject
				Default body
			No email configured
		Forced updates
		Pull-to-refresh bar
			Updates timestamp
			Makes proper network request
		Webviews
	Network
		3G
		EDGE
		Wi-Fi
		Airplane Mode
		Simulated poor connection
			On first launch
			Error message on timeouts
			Doesn't block main thread
	Data
		Preserved through data model changes
		Time Settings
			Properly display all time zones
			Relative time displays
			Switching between time zones
			System time too fast/slow
		Internationalization
			Supported languages?
			Long translated strings fit
			Localized text in images
			Region formats
	Software
		iOS
			4.x
			5.x
			6.x
		Crash reporting
		Analytics
		Memory Warnings
			No crashes
			View state is preserved
		Upgrades
			Upgrade path from all previous versions
			User settings are preserved
				Push notifications
				Credentials
				User preferences
		Backgrounding
			App closes in time
			App state preserved on next launch
		Gestures
	Audio / Video
		AirPlay
		Background audio
		Second screen
		Pauses other audio


### 效果图
![iOS-Testing-Mind-Map-12](images/iOS-Testing-Mind-Map-12.png)

### 备注
需要根据实际需要和测试人员一起确认适合各App的详单

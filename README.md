# wlmedia
android 音视频播放SDK，几句代码即可实现音视频播放功能~
#### 功能
##### **支持：http、https、rtsp、rtp、rtmp、byte[]、加密视频和各种文件格式视频；
##### **截图、音轨选择、自定义视频滤镜、变速变调、声道切换、无缝切换surface（surfaceview和textureview）、视频比例设置等；


## 1、Usage

### Gradle: [ ![Download](https://api.bintray.com/packages/ywl5320/maven/wlmedia/images/download.svg?version=1.0.0) ](https://bintray.com/ywl5320/maven/wlmedia/1.0.0/link)

    implementation 'ywl.ywl5320:wlmedia:1.0.0'


## 2、实例图片

### 播放视频
<img width="360" height="640" src="https://github.com/wanliyang1990/wlmedia/blob/master/img/demo.png"/>


## 3、调用方式

### 配置NDK编译平台:

	defaultConfig {
		...
		ndk {
		    abiFilter("arm64-v8a")
		    abiFilter("armeabi-v7a")
		    abiFilter("x86")
		    abiFilter("x86_64")
		}
		...
	}
	
### 基本权限

	<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.INTERNET"/>

### 接入代码（SDK API level:28）

	// WlSurfaceView 一般播放使用
	<com.ywl5320.wlmedia.surface.WlSurfaceView
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
    
	// WlTextureView 需要做透明、移动、旋转等使用
    <com.ywl5320.wlmedia.surface.WlTextureView
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
	
	WlMedia wlMedia = new WlMedia();// 可支持多实例播放（主要对于音频，视频实际验证效果不佳）
	wlSurfaceView.setWlMedia(wlMedia);//给视频surface设置播放器
	
	//异步准备完成后开始播放
	wlMedia.setOnPreparedListener(new WlOnPreparedListener() {
            @Override
            public void onPrepared() {
                wlMedia.start();//开始播放
            }
        });
	
	//设置url源
	wlMedia.setSource("/storage/sdcard1/fcrs.1080p.mp4");
	wlMedia.prepared();//异步准备
	
## 4、其他API
	待完善

## 5、参考资料

### [我的视频课程（基础）：《（NDK）FFmpeg打造Android万能音频播放器》](https://edu.csdn.net/course/detail/6842)
### [我的视频课程（进阶）：《（NDK）FFmpeg打造Android视频播放器》](https://edu.csdn.net/course/detail/8036)
### [我的视频课程（编码直播推流）：《Android视频编码和直播推流》](https://edu.csdn.net/course/detail/8942)
### [我的视频课程（C++ OpenGL）：《Android C++ OpenGL》](https://edu.csdn.net/course/detail/19367)

#### create By：ywl5320 2019-12-16






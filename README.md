# wlmedia
android 音视频播放SDK，几句代码即可实现音视频播放功能~
#### 功能
##### **支持：http、https、rtsp、rtp、rtmp、byte[]、加密视频和各种文件格式视频；
##### **截图、音轨选择、自定义视频滤镜、变速变调、声道切换、无缝切换surface（surfaceview和textureview）、视频比例设置等；


## 1、Usage

### Gradle: [ ![Download](https://api.bintray.com/packages/ywl5320/maven/wlmedia/images/download.svg?version=1.0.1) ](https://bintray.com/ywl5320/maven/wlmedia/1.0.1/link)

    implementation 'ywl.ywl5320:wlmedia:1.0.1'


## 2、实例图片

<img width="360" height="640" src="https://github.com/wanliyang1990/wlmedia/blob/master/img/wlmedia.gif"/><br/>
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

## 4、接入代码

#### 4.1、视频surface选择
	// WlSurfaceView 一般播放使用
	<com.ywl5320.wlmedia.surface.WlSurfaceView
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
    
	// WlTextureView 需要做透明、移动、旋转等使用
    <com.ywl5320.wlmedia.surface.WlTextureView
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
        
#### 4.2、创建播放器

##### 4.2.1 播放视频
	
	WlMedia wlMedia = new WlMedia();
    wlMedia.setPlayModel(WlPlayModel.PLAYMODEL_AUDIO_VIDEO);//同时播放音频视频
	wlSurfaceView.setWlMedia(wlMedia);//给视频surface设置播放器
	
	//异步准备完成后开始播放
	wlMedia.setOnPreparedListener(new WlOnPreparedListener() {
            @Override
            public void onPrepared() {
                wlMedia.start();//开始播放
            }
        });
    //surface初始化好了后 开始播放视频（用于一打开activity就播放场景）
    wlSurfaceView.setOnVideoViewListener(new WlOnVideoViewListener() {
            @Override
            public void initSuccess() {
                wlMedia.setSource("/storage/sdcard1/fcrs.1080p.mp4");//设置数据源
                wlMedia.prepared();//异步开始准备播放
            }

            @Override
            public void moveSlide(double value) {

            }

            @Override
            public void movdFinish(double value) {
                wlMedia.seek(value);//seek
            }
        });
    //自定义滤镜方式
    String fs = "precision mediump float;" +
                "varying vec2 ft_Position;" +
                "uniform sampler2D sTexture; " +
                "void main() " +
                "{ " +
                "vec4 v=texture2D(sTexture, ft_Position); " +
                "float average = (v.r + v.g + v.b) / 3.0;" +
                "gl_FragColor = vec4(average, average, average, v.a);" +
                "}";
    wlMedia.setfShader(fs);
    wlMedia.changeFilter();
    
##### 4.2.2 播放音频
    WlMedia wlMedia = new WlMedia();
    wlMedia.setPlayModel(WlPlayModel.PLAYMODEL_ONLY_AUDIO);//设置只播放音频（必须）
    wlMedia.setSource(WlAssetsUtil.getAssetsFilePath(this, "mydream.m4a"));//设置数据源
    wlMedia.setOnPreparedListener(new WlOnPreparedListener() {
        @Override
        public void onPrepared() {
            wlMedia.start();
        }
    });
    wlMedia.prepared();
##### 4.2.3 播放加密视频文件
    
    WlMedia wlMedia = new WlMedia();
    wlMedia.setPlayModel(WlPlayModel.PLAYMODEL_AUDIO_VIDEO);
    wlSurfaceView.setWlMedia(wlMedia);
    wlMedia.setBufferSource(true, true);//必须都为true
    wlMedia.setOnDecryptListener(new WlOnDecryptListener() {
    
        //解密算法
        @Override
        public byte[] decrypt(byte[] encrypt_data) {
            int length = encrypt_data.length;
            for(int i = 0; i < length; i++)
            {
                encrypt_data[i] = (byte) ((int)encrypt_data[i] ^ 666);
            }
            WlLog.d("decrypt");
            return encrypt_data;
        }
    });
    wlSurfaceView.setOnVideoViewListener(new WlOnVideoViewListener() {
            @Override
            public void initSuccess() {
                wlMedia.setSource(WlAssetsUtil.getAssetsFilePath(WlEncryptActivity.this, "fhcq-ylgzy-dj-encrypt.mkv"));//加密视频源
                wlMedia.prepared();
            }

            @Override
            public void moveSlide(double value) {

            }

            @Override
            public void movdFinish(double value) {
                wlMedia.seek(value);
            }
        });
##### 4.2.4 播放byte[]音视频数据
    wlMedia = new WlMedia();
    wlMedia.setBufferSource(true, false);//必须第一个为true,第二个为false
    wlMedia.setPlayModel(WlPlayModel.PLAYMODEL_ONLY_VIDEO);//根据byte类型来设置（可以音频、视频、音视频）
    wlTextureView.setWlMedia(wlMedia);
    wlMedia.setOnPreparedListener(new WlOnPreparedListener() {
            @Override
            public void onPrepared() {
                wlMedia.start();
            }
        });
    wlTextureView.setOnVideoViewListener(new WlOnVideoViewListener() {
        @Override
        public void initSuccess() {
            new Thread(new Runnable() {
                long length = 0;
                @Override
                public void run() {
                    try {
                        //从文件模拟byte[]数据
                        File file = new File(WlAssetsUtil.getAssetsFilePath(WlBufferActivity.this, "mytest.h264"));
                        FileInputStream fi = new FileInputStream(file);
                        byte[] buffer = new byte[1024 * 64];
                        int buffersize = 0;
                        int bufferQueueSize = 0;
                        exit = false;
                        while(true)
                        {
                            if(exit)
                            {
                                break;
                            }
                            if(wlMedia.isPlay())
                            {
                                WlLog.d("read thread " + bufferQueueSize);
                                if(bufferQueueSize < 20)//控制队列大小，不然内存可能会增大溢出
                                {
                                    buffersize = fi.read(buffer);
                                    if(buffersize <= 0)
                                    {
                                        WlLog.d("read thread ==============================  read buffer exit ...");
                                        wlMedia.putBufferSource(null, -1);
                                        break;
                                    }
                                    bufferQueueSize = wlMedia.putBufferSource(buffer, buffersize);//返回值为底层buffer队列个数
                                    while(bufferQueueSize < 0)
                                    {
                                        bufferQueueSize = wlMedia.putBufferSource(buffer, buffersize);
                                    }
                                }
                                else
                                {
                                    bufferQueueSize = wlMedia.putBufferSource(null, 0);
                                }
                                sleep(10);
                            }
                            else
                            {
                                WlLog.d("buffer exit");
                                break;
                            }

                        }
                        wlMedia.stop();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }).start();
            wlMedia.prepared();
        }

        @Override
        public void moveSlide(double value) {

        }

        @Override
        public void movdFinish(double value) {

        }
    });
    


	
	
## 5、其他API
	待完善

## 6、参考资料

### [我的视频课程（基础）：《（NDK）FFmpeg打造Android万能音频播放器》](https://edu.csdn.net/course/detail/6842)
### [我的视频课程（进阶）：《（NDK）FFmpeg打造Android视频播放器》](https://edu.csdn.net/course/detail/8036)
### [我的视频课程（编码直播推流）：《Android视频编码和直播推流》](https://edu.csdn.net/course/detail/8942)
### [我的视频课程（C++ OpenGL）：《Android C++ OpenGL》](https://edu.csdn.net/course/detail/19367)

### Create By：ywl5320 2019-12-16






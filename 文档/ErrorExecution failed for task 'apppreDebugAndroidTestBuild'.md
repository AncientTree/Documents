# Android Studio ErrorExecution failed for task 'apppreDebugAndroidTestBuild'

这是`/ProjectName/app/bulid.gradle`的全部内容：
```Groovy
apply plugin: 'com.android.application'

android {
    compileSdkVersion 27
    buildToolsVersion "27.0.3"  //A. 这一行是我自己添加的

    defaultConfig {
        applicationId "com.example.pengzhw.myapplication"
        minSdkVersion 25
        targetSdkVersion 27
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:27.1.1'  //B. 这一行把"26.X.X改成了27.1.1"
    implementation 'com.android.support.constraint:constraint-layout:1.1.0'
    implementation 'com.android.support:design:27.1.1'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
    androidTestCompile 'com.android.support:support-annotations:27.1.1'  //C. 这一行把"26.X.X改成了27.1.1"
}

```

如果B、C两个地方不改就会报错如题。
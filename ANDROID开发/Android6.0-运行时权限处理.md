为什么需要有运行时权限？

大家都知道在Android6.0之前，权限在应用安装过程中只询问一次，以列表的形式展现给用户，如果点击取消（即不认可应用所申请的权限），则会取消应用的安装。而用户出于安装应用的需求，一般都会点击同意，而应用就有可能在后台进行一些非法操作。

在Android6.0版本以后，推出了运行时权限功能。即用户不需要在安装应用的时候一次性授权，而是可以在应用的使用过程中对某一项权限进行授权。

当然并不是所有的权限都不需要手动动态申请，Android将权限分为了普通权限和高危权限。对于普通权限，系统会自动完成授权，我们只需要处理高危权限的授权即可。下面就是Android的危险权限表。



| **Permission Group**                                         | **Permissions**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [CALENDAR](https://developer.android.com/reference/android/Manifest.permission_group.html#CALENDAR) | [READ_CALENDAR](https://developer.android.com/reference/android/Manifest.permission.html#READ_CALENDAR)[WRITE_CALENDAR](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CALENDAR) |
| [CAMERA](https://developer.android.com/reference/android/Manifest.permission_group.html#CAMERA) | [CAMERA](https://developer.android.com/reference/android/Manifest.permission.html#CAMERA) |
| [CONTACTS](https://developer.android.com/reference/android/Manifest.permission_group.html#CONTACTS) | [READ_CONTACTS](https://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)[WRITE_CONTACTS](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CONTACTS)[GET_ACCOUNTS](https://developer.android.com/reference/android/Manifest.permission.html#GET_ACCOUNTS) |
| [LOCATION](https://developer.android.com/reference/android/Manifest.permission_group.html#LOCATION) | [ACCESS_FINE_LOCATION](https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_FINE_LOCATION)[ACCESS_COARSE_LOCATION](https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_COARSE_LOCATION) |
| [MICROPHONE](https://developer.android.com/reference/android/Manifest.permission_group.html#MICROPHONE) | [RECORD_AUDIO](https://developer.android.com/reference/android/Manifest.permission.html#RECORD_AUDIO) |
| [PHONE](https://developer.android.com/reference/android/Manifest.permission_group.html#PHONE) | [READ_PHONE_STATE](https://developer.android.com/reference/android/Manifest.permission.html#READ_PHONE_STATE)[CALL_PHONE](https://developer.android.com/reference/android/Manifest.permission.html#CALL_PHONE)[READ_CALL_LOG](https://developer.android.com/reference/android/Manifest.permission.html#READ_CALL_LOG)[WRITE_CALL_LOG](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CALL_LOG)[ADD_VOICEMAIL](https://developer.android.com/reference/android/Manifest.permission.html#ADD_VOICEMAIL)[USE_SIP](https://developer.android.com/reference/android/Manifest.permission.html#USE_SIP)[PROCESS_OUTGOING_CALLS](https://developer.android.com/reference/android/Manifest.permission.html#PROCESS_OUTGOING_CALLS) |
| [SENSORS](https://developer.android.com/reference/android/Manifest.permission_group.html#SENSORS) | [BODY_SENSORS](https://developer.android.com/reference/android/Manifest.permission.html#BODY_SENSORS) |
| [SMS](https://developer.android.com/reference/android/Manifest.permission_group.html#SMS) | [SEND_SMS](https://developer.android.com/reference/android/Manifest.permission.html#SEND_SMS)[RECEIVE_SMS](https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_SMS)[READ_SMS](https://developer.android.com/reference/android/Manifest.permission.html#READ_SMS)[RECEIVE_WAP_PUSH](https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_WAP_PUSH)[RECEIVE_MMS](https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_MMS) |
| [STORAGE](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) | [READ_EXTERNAL_STORAGE](https://developer.android.com/reference/android/Manifest.permission.html#READ_EXTERNAL_STORAGE)[WRITE_EXTERNAL_STORAGE](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_EXTERNAL_STORAGE) |

 

注意：表格中的每一个危险权限都属于一个权限组，我们在进行运行时权限处理时使用的是权限名，但当用户一旦同意授权，那么该权限所对应的权限组的所有其他权限都会被同时授权。

即一旦READ_CALENDAR被授权了，应用也有WRITE_CALENDAR权限了。



运行时权限处理

　　对于运行在API23系统的应用，如果它的targetAPI大于等于23，则需要对运行时权限进行申请，否则会出现抛出异常，导致程序crash。

　　而在现有的 代码中加入运行时权限处理也很简单，只需要在需要申请危险权限的地方嵌套权限申请的代码即可，下面就是一个典型的运行时权限申请的代码。





```
package com.example.playaudiotest;

import android.Manifest;
import android.content.pm.PackageManager;
import android.media.MediaPlayer;
import android.os.Environment;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import java.io.File;

public class MainActivity extends AppCompatActivity implements View.OnClickListener{

    private MediaPlayer mediaPlayer = new MediaPlayer();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button play = (Button) findViewById(R.id.play);
        Button pause = (Button) findViewById(R.id.pause);
        Button stop = (Button) findViewById(R.id.stop);
        play.setOnClickListener(this);
        pause.setOnClickListener(this);
        stop.setOnClickListener(this);
        if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{ Manifest.permission. WRITE_EXTERNAL_STORAGE }, 1);
        } else {
            initMediaPlayer(); // 初始化MediaPlayer
        }
    }

    private void initMediaPlayer() {
        try {
            File file = new File(Environment.getExternalStorageDirectory(), "music.mp3");
            mediaPlayer.setDataSource(file.getPath()); // 指定音频文件的路径
            mediaPlayer.prepare(); // 让MediaPlayer进入到准备状态
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        switch (requestCode) {
            case 1:
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    initMediaPlayer();
                } else {
                    Toast.makeText(this, "拒绝权限将无法使用程序", Toast.LENGTH_SHORT).show();
                    finish();
                }
                break;
            default:
        }
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.play:
                if (!mediaPlayer.isPlaying()) {
                    mediaPlayer.start(); // 开始播放
                }
                break;
            case R.id.pause:
                if (mediaPlayer.isPlaying()) {
                    mediaPlayer.pause(); // 暂停播放
                }
                break;
            case R.id.stop:
                if (mediaPlayer.isPlaying()) {
                    mediaPlayer.reset(); // 停止播放
                    initMediaPlayer();
                }
                break;
            default:
                break;
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (mediaPlayer != null) {
            mediaPlayer.stop();
            mediaPlayer.release();
        }
    }

}
```










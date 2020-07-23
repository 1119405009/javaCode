

# Notification的作用

Notification是一种全局效果的通知，在系统的通知栏中显示。既然作为通知，其基本作用有：

-  显示接收到短消息、即时信息等
-  显示客户端的推送（广告、优惠、新闻等）
-  显示正在进行的事物（后台运行的程序，如音乐播放进度、下载进度）

 

# Notification的基本操作

Notification的基本操作主要有创建、更新和取消三种。一个Notification的必要属性有三项，如果不设置的话在运行时会抛出异常：

1. 小图标，通过setSmallIcon方法设置
2. 标题，通过setContentTitle方法设置
3. 内容，通过setContentText方法设置。

除了以上三项，其他均为可选项，不过一般而言，通知需要有交互的功能，所以一般Notification具有Action属性，这样就能跳转到App的某一个Activity、启动一个service或者发送一个Broadcast。

当系统受到通知时，可以通过震动、铃声、呼吸灯等多种方式进行提醒。



下面就从Notification的基本操作逐条介绍：

## Notification的创建

Notification的创建过程主要涉及到Notification.Builder、Notification、NotificationManager

Notification.Builder：

​     使用建造者模式构建Notification对象。由于Notification.Builder仅支持Android4.1及之后的版本，为了解决兼容性的问题，使用V4兼容库中的NotifivationCompat.Builder类。

Notification：通知对应类，保存通知相关的数据。NotificationManager向系统发送通知时会用到。

NotificationManager：通知管理类，调用NotificationManager的notify方法可以向系统发送通知。

获取 NotificationManager 对象：

NotificationManager notificationManager = (NotificationManager) getSystemService(Context.*NOTIFICATION_SERVICE*);

前面讲到，Notification有三个必要属性以及一个很有必要的属性Action。下面我们就创建一个简单的Notification，主要有以下三步：

1. 获取NotificationManager实例
2. 实例化NotificationCompat.Builder并设置相关属性
3. 通过builder.build方法来生成Notification对象，并发送通知



```
private void sendNotification(){

    Intent intent = new Intent(this,SettingsActivity.class);
    
    PendingIntent mPendingIntent = PendingIntent.getActivity(this,0,intent,PendingIntent.FLAG_ONE_SHOT);
    
    NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
    
    NotificationCompat.Builder builder = (NotificationCompat.Builder) new NotificationCompat.Builder(this)
    
            //设置小图标
    
            .setSmallIcon(R.mipmap.ic_launcher)
    
            //点击后自动清除
    
            .setAutoCancel(true)
    
            //设置通知标题
    
            .setContentTitle("最简单的通知")
    
            //设置通知内容
    
            .setContentText("真的很简单很简单很简单")
    
            //设置通知的动作
    
            .setContentIntent(mPendingIntent)
    
            //设置通知时间，默认为系统发出通知的时间
    
            .setWhen(System.currentTimeMillis());
    
            //第一个参数为Notification的id
    
    notificationManager.notify(2,builder.build());

}
```



PendingIntent支持三种待定的意图：启动Activity，启动Service和发送Broadcast。对应于它的三个接口方法。

| static PendingIntent | getActivity(Context context,int requestCode,Intent intent,int flags)获取一个PendingIntent，该意图发生时，相当于Context.startActivity(Intent) |
| -------------------- | ------------------------------------------------------------ |
| static PendingIntent | getService (Context context,int requestCode,Intent intent,int flags)获取一个PendingIntent，该意图发生时，相当于Context.startService (Intent) |
| static PendingIntent | getBroadcast(Context context,int requestCode,Intent intent,int flags)获取一个PendingIntent，该意图发生时，相当于Context.sendBroadcast(Intent) |

其中context和intent不需要讲，主要说一下requestCode和flags。其中requestCode是PendingIntent发送发的请求码，多数情况下设置为0即可，requestCode会影响到flags的效果。

PendingIntent相同：Intent相同且requestCode也相同。（Intent相同需要ComponentName和intent-filter相同）

flags的常见类型有：

FLAG_ONE_SHOT：只能被使用一次，然后就会被自动cancel，如果后续还有相同的PendingIntent。那么他们的send方法就会调用失败。
FLAG_NO_CREATE：如果当前系统中不存在相同的PendingIntent对象，系统不会创建该PendingIntent对象，而是直接返回null。（很少使用）

FLAG_CANCEL_CURRENT：如果当前系统中已经存在一个相同的 PendingIntent 对象，那么就将先将已有的 PendingIntent 取消，然后重新生成一个 PendingIntent 对象。

FLAG_UPDATE_CURRENT：当前描述的PendingIntent如果已经存在，那么它们会被更新，即Intent中的Extras会被替换到最新的。

 

## Notification的更新

更新通知的操作很简单，只需要再次发送一次相同ID的通知即可，如果之前的通知还没有被取消，则会直接更新该通知相关的属性；如果之前的通知已经被取消，则会重新创建一个新的通知。

更新通知和发送通知采用同样的方法。

 

## Notification的取消

取消通知的方式主要有以下5种：

1. 点击通知栏的清除按钮，会清除所有可清除的通知
2. 设置了setAutoCancel()或者设置了flags为FLAG_AUTO_CANCEL的通知，点击通知时会自动清除。
3. 通过NotificationManager调用cancel(int id)来取消指定id的通知
4. 通过NotificationManager调用cancel(String tag,int id)方法清除指定Tag和ID的通知。
5. 通过NotificationManager调用cancelAll()清除所有该应用之前发送的通知

如果是通过NotificationManager.notify(String tag, int id, Notification notify) 方法创建的通知，那么只能通过 NotificationManager.cancel(String tag, int id) 或cancelAll()方法才能清除对应的通知，调用NotificationManager.cancel(int id) 无效。

 

## Notification的通知效果

前面提到了Notification的通知效果，有了通知效果更能提醒用户去查看Notification。

Notification的通知效果有震动、呼吸灯、铃声三种，可以通过builder中的setDefaults（int defaults）方法来设置，属性有以下四种，一旦设置了默认效果，自定义效果就会失效。





```
//添加默认震动效果,需要申请震动权限

//<uses-permission android:name="android.permission.VIBRATE" />

Notification.DEFAULT_VIBRATE

//添加系统默认声音效果，设置此值后，调用setSound()设置自定义声音无效

Notification.DEFAULT_SOUND

//添加默认呼吸灯效果，使用时须与 Notification.FLAG_SHOW_LIGHTS 结合使用，否则无效

Notification.DEFAULT_LIGHTS

//添加上述三种默认提醒效果

Notification.DEFAULT_ALL
```





#### 铃声

```


//调用系统默认响铃,设置此属性后setSound()会无效

//.setDefaults(Notification.DEFAULT_SOUND)

//调用系统多媒体裤内的铃声

//.setSound(Uri.withAppendedPath(MediaStore.Audio.Media.INTERNAL_CONTENT_URI,"2"));

//调用自己提供的铃声，位于 /res/values/raw 目录下

.setSound(Uri.parse("android.resource://com.littlejie.notification/" + R.raw.sound))
```





#### 震动

```
long[] vibrate = new long[]{0, 500, 1000, 1500};


//使用系统默认的震动参数,会与自定义的冲突

//.setDefaults(Notification.DEFAULT_VIBRATE)

//自定义震动效果

.setVibrate(vibrate);
```



#### 呼吸灯



```
//ledARGB 表示灯光颜色、 ledOnMS 亮持续时间、ledOffMS 暗的时间

.setLights(0xFF0000, 3000, 3000);
```





# Notification的自定义布局

上面讲到的Notification的布局都是系统默认的，当然有时候处于需求，我们可能需要自定义Notification的布局。

那如何实现Notification的自定义布局呢？

这里就需要提出一个新的知识点RemoteView，望文生义，即远程View。

RemoteView表示的是一种View结构，它可以在其他进程中显示（具体来讲是SystemServer进程），由于它是在其他进程中显示，为了更新它的界面，我们不能简单地使用普通View的那一套方法，RemoteView提供了一系列Set方法用于更新界面。





```
package com.pignet.remoteviewtest;

import android.app.Notification;

import android.app.NotificationManager;

import android.app.PendingIntent;

import android.content.Context;

import android.content.Intent;

import android.support.v7.app.AppCompatActivity;

import android.os.Bundle;

import android.view.View;

import android.widget.Button;

import android.widget.RemoteViews;

public class MainActivity extends AppCompatActivity {


    @Override
    
    protected void onCreate(Bundle savedInstanceState) {
    
        super.onCreate(savedInstanceState);
    
        setContentView(R.layout.activity_main);
    
        Button btnNotification = (Button) findViewById(R.id.btn_notification);
    
        btnNotification.setOnClickListener(new View.OnClickListener() {
    
            @Override
    
            public void onClick(View v) {
    
                sendNotification();
    
            }
    
        });
    
    }
    
    private void sendNotification(){
    
        NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
    
        Notification notification =new Notification();
    
        notification.icon=R.mipmap.ic_launcher;
    
        notification.when=System.currentTimeMillis();
    
        notification.flags=Notification.FLAG_AUTO_CANCEL;
    
        //跳转意图
    
        Intent intent = new Intent(this,SettingsActivity.class);
    
        //建立一个RemoteView的布局，并通过RemoteView加载这个布局
    
        RemoteViews remoteViews = new RemoteViews(getPackageName(),R.layout.layout_notification);
    
        //为remoteView设置图片和文本
    
        remoteViews.setTextViewText(R.id.message,"第一条通知");
    
        remoteViews.setImageViewResource(R.id.image,R.mipmap.ic_launcher_round);
    
        //设置PendingIntent
    
        PendingIntent pendingIntent = PendingIntent.getActivity(this,0,intent,PendingIntent.FLAG_UPDATE_CURRENT);
    
        //为id为openActivity的view设置单击事件
    
        remoteViews.setOnClickPendingIntent(R.id.openActivity,pendingIntent);
    
        //将RemoteView作为Notification的布局
    
        notification.contentView =remoteViews;
    
        //将pendingIntent作为Notification的intent，这样当点击其他部分时，也能实现跳转
    
        notification.contentIntent=pendingIntent;
    
        notificationManager.notify(1,notification);
    
    }

}

```


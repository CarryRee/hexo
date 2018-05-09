---
title: Interruption
date: 2018-05-09 10:19:20
tags:
---

# iOS监听中断方法

有时候播放音乐的时候会被电话或打开其他播放器，这个时候声音会被抢占，这个时候则需要监听中断

## 注册通知
```
[[NSNotificationCenter defaultCenter] addObserver:self
                                         selector:@selector(audioSessionInterruptionNotification:)
                                             name:AVAudioSessionInterruptionNotification
                                           object:nil];

```

## 通知方法
```
// 监听中断通知调用的方法
- (void)audioSessionInterruptionNotification:(NSNotification *)notification {
    int type = [notification.userInfo[AVAudioSessionInterruptionOptionKey] intValue];

    switch (type) {
        case AVAudioSessionInterruptionTypeBegan: // 被打断
            break;
        case AVAudioSessionInterruptionTypeEnded: // 中断结束
            break;
        default:
            break;
    }
}
```

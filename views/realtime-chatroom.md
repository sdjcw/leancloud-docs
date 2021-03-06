{% import "views/_im.md" as imPartial %}

# 即时通信 - 聊天室开发指南

{{ imPartial.gettingStarted() }}

聊天室是专门针对网络互动直播，在线比赛直播，文字直播等业务场景提供的抽象概念，它具备无人数上限，超大消息并发量的特性，同时相对于传统对话，它舍弃了多余的一些事件通知，例如没有人员变动的通知，减少多余了网络传输量。

## AVIMChatRoom

它的大概使用流程是这样的：

![realtime-chatroom-seq](images/realtime-chatroom-seq.svg)

## 创建聊天室

```objc
 AVIMClient *client = [[AVIMClient alloc] initWithClientId:@"Tom"];
    
    [client openWithCallback:^(BOOL success, NSError *error) {
        
        if (success && !error) {
            
            [client createChatRoomWithName:@"聊天室"
                                attributes:nil
                                  callback:
             ^(AVIMChatRoom *chatRoom, NSError *error) {
                 
                 if (chatRoom && !error) {
                     
                     AVIMTextMessage *textMessage = [AVIMTextMessage messageWithText:@"这是一条消息"
                                                                          attributes:nil];
                     
                     [chatRoom sendMessage:textMessage callback:^(BOOL success, NSError *error) {
                         
                         if (success && !error) {
                             
                             // send message success.
                         }
                     }];
                 }
             }];
        }
    }];
```
```java
AVIMChatRoom room = (AVIMChatRoom) LCChatKit.getInstance().getClient().getChatRoom("conversationId");
room.quit(new AVIMConversationCallback() {
@Override
public void done(AVIMException ex) {
    if (null != ex) {
    showToast(ex.getMessage());
    } else {
    ;
    }
}
});
```
```js
```

## 查询聊天室

```objc
AVIMClient *client = [[AVIMClient alloc] initWithClientId:@"Tom"];

[client openWithCallback:^(BOOL success, NSError *error) {
    
    if (success && !error) {
        
        [client.conversationQuery getConversationById:@"Chat Room ID" callback:^(AVIMConversation *chatRoom, NSError *error) {
            
            if (chatRoom && [chatRoom isKindOfClass:[AVIMChatRoom class]] && !error) {
                
                // query success.
            }
        }];
    }
}];
```
```java
AVIMConversationsQuery query = imClient.getChatRoomQuery();
query.whereEqualTo("name", "天南海北聊天室");
query.findInBackground(new AVIMConversationQueryCallback() {
@Override
public void done(List<AVIMConversation> conversations, AVIMException e) {
    if (null != e) {
    showToast(e.getMessage());
    } else {
    // get results.
    }
}
});
```
```js
```

## 加入聊天室


注意，一个 client id 在一个应用内同一时间只允许存在于一个聊天室，只要他再次加入别的聊天室，他就会自动离开上一个聊天室，不再接收到上一个聊天室产生的新消息，这个很好理解，比如你在斗鱼直播，除非你在手机上只可能同时观看一个直播，不可以同时进入两个直播间。

## 接收消息

{{ imPartial.receivedMessage() }}


## 退出聊天室

当然前面已经说过，只要你加入新的聊天室，服务的端自然会帮你退出旧的聊天室，但是有一些情况是，客户端就只想退出聊天室，SDK 也提供了相应的接口：

```objc
AVIMClient *client = [[AVIMClient alloc] initWithClientId:@"Tom"];
    
    [client openWithCallback:^(BOOL success, NSError *error) {
        
        if (success && !error) {
            
            [client.conversationQuery getConversationById:@"Chat Room ID" callback:^(AVIMConversation *chatRoom, NSError *error) {
                
                if (chatRoom && [chatRoom isKindOfClass:[AVIMChatRoom class]] && !error) {
                    
                    [chatRoom quitWithCallback:^(BOOL success, NSError *error) {
                        
                        if (success && !error) {
                            
                            // quit success.
                        }
                    }];
                }
            }];
        }
    }];
```
```java
AVIMChatRoom room = (AVIMChatRoom) LCChatKit.getInstance().getClient().getChatRoom("conversationId");
      room.quit(new AVIMConversationCallback() {
        @Override
        public void done(AVIMException ex) {
          if (null != ex) {
            showToast(ex.getMessage());
          } else {
            ;
          }
        }
      });
```
```js
```


## FAQ

Q: 如何确保聊天室的消息全量送达？
A: 这一点请参考我们的[消息分级](/realtime_guide-objc.html#消息等级)


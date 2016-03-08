# RingCentral WebPhone Library

## Installation

```ssh
npm install ringcentral-web-phone
// or
bower install ringcentral-web-phone
```

## Demo

```sh
git clone https://github.com/ringcentral/ringcentral-web-phone.git
npm install
bower install
```

WebRTC works with issues when served from file system directly to browser (e.g. `file://` protocol), you will need a
local HTTP server. If you don't have local HTTP server, please install it as well:

```sh
$ sudo npm install http-server -g
$ http-server
```

1. Open localhost:8080/demo/ in the browser
2. Add your RC credentials and click on `Register Sip Configurations` .
3. For making outbound calls, enter phone number and click on call. To disconnect to call, click on `Disconnect Call`.
4. For recieve incoming calls, Click on Accept button when window pops up (will be visible when there is an incoming call).

The demo app uses the sandbox environment. If there's any connection problems, you may need to switch to the  production environment.

Functionalities included:

1. Sip Register/UnRegister
2. Make Outgoing Calls
3. Accept Incoming calls
4. Hold/UnHold calls
5. Mute/Unmute calls
6. Transfer calls
7. Record/Stop recording calls
8. Flip, park calls
9. Send DTMF
10. Forward incoming calls

## Usage

### Include Library And HTML Elements

```html
<video id="remoteVideo" hidden="hidden"></video>
<video id="localVideo" hidden="hidden" muted="muted"></video>

<script src=".../ringcentral-bundle.js" type="text/javascript"></script>
<script src=".../ringcentral-web-phone.js" type="text/javascript"></script>
```

### Application

Configure the web-phone:

```js
var sdk = new RingCentral.SDK({
    appKey: '...',
    appSecret: '...',
    server: RingCentral.SDK.server.production // or .sandbox
});

var platform = sdk.platform();

platform
    .login({
        username: '...',
        password: '...'
    })
    .then(function() {
    
        return platform
            .post('/client-info/sip-provision', {
                sipInfo: [{transport: 'WSS'}]
            })
            .then(function(res) {
                return new RingCentral.WebPhone(res.json());
            });
        
    })
    .then(function(webPhone){
    
        // YOUR CODE HERE
    
    });
```

### API

Except for some RingCentral-specific features the API is 100% the same as SIP.JS: http://sipjs.com/api/0.7.0

#### Initiating The Call

```javascript
webPhone.userAgent.invite('...', {
    media: {
        constraints: {
            audio: true,
            video: false
        },
        render: {
            remote: document.getElementById('remoteVideo'),
            local: document.getElementById('localVideo')
        }
    }
});
```

#### Accepting Incoming Call

```javascript
webPhone.userAgent.on('invite', function(session){
    session.accept({
        media: {
            render: {
                remote: document.getElementById('remoteVideo'),
                local: document.getElementById('localVideo')
            }
        }
    });
});
```

#### DTMF *RC-Specific*

Callee will be put on hold and the another person can join into the call by dialing the extension number announced within the call.

```js
webPhone.dtmf(session, 'digits').then(...);
```

#### Hold/Unhold *RC-Specific*

Callee will be put on hold and the another person can join into the call by dialing the extension number announced within the call.

```js
webPhone.hold(session).then(...);
webPhone.unhold(session).then(...);
```

#### Park *RC-Specific*

Callee will be put on hold and the another person can join into the call by dialing the extension number announced within the call.

```js
webPhone.park(session).then(...);
```

#### Flip *RC-Specific*

Caller can filp calls to different devices logged in through the same credentials.

```js
webPhone.flip(session, 'TARGET_NUMBER').then(...);
```

#### Barge/Whisper *RC-Specific*

Not yet implemented. Could be done by dialing \*83. The account should be enabled for barge/whisper access through system admin.
# Client communication

You can safely enable cross-origin communication between the parent dom (the "client") and the child dom containing the IBM Planning Analytics Workspace UI widget. Communication between the client and the child dom is required in order for the client to be able to issue commands or respond to specific Planning Analytics Workspace UI widget events.

The Planning Analytics Workspace UI widgets are usually contained inside of an iframe. The approach documented in this section is safe for same-origin and cross-origin communication.

<aside class="notice">
You can embed the Planning Analytics Workspace UI API in an iframe for Planning Analytics Workspace Local, but you cannot embed the API in an iframe for Planning Analytics Workspace on Cloud. To embed the Planning Analytics Workspace UI API in an iframe for Planning Analytics Workspace Local, you must enable CORS. For more information on how you can enable CORS for Planning Analytics Workspace Local, see <a href='https://www-03preprod.ibm.com/support/knowledgecenter/SSD29G_2.0.0_test/com.ibm.swg.ba.cognos.tm1_inst.2.0.0.doc/c_paw_config_file.html'>Configure parameters</a>.
</aside>

Sending and receiving messages from the client are accomplished using the `Window.postMessage` API. <a href='https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage'>See this link for more information</a>.

In the code examples below, replace `<production url>` with your own production URL.

# Sending messages

> Examples:

```shell
var iframe = document.getElementByd('iframe_id_here');
// check if widget is fully loaded before sending message
// isWidgetReady is set to true when client receives the "onWidgetLoaded" message
if(isWidgetReady && iframe.contentWindow) {
    iframe.contentWindow.postMessage({
        type: "execute",
        eventName: "Compute",
        eventPayload: {}
    }, "<production url>");
}
```

> Subscribes to the tm1mdv:executeCommand event. Once subscribed, the client will receive messages whenever that event occurs

```shell
iframe.contentWindow.postMessage({
    type: "subscribe",
    eventName: "tm1mdv:executeCommand",
    eventPayload: {
        name: "first subscription"
    }
}, "<production url>");
```

> Unsubscribes to the event above.

```shell
iframe.contentWindow.postMessage({
    type: "unsubscribe",
    eventName: "tm1mdv:executeCommand",
    eventPayload: {
        name: "first subscription"
    }
}, "<production url>");
```

> Triggers an redraw event.

```shell
iframe.contentWindow.postMessage({
    type: 'trigger',
    eventName: 'tm1mdv:redraw'
}, apiOrigin);
```

The following is a list of parameters that can be passed in:


Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of message to send. | - `trigger` <br> - `subscribe` <br> - `unsubscribe` <br> - `execute`
eventName | Yes | The full name of the event. | - `tm1mdv:executeCommand` <br> - `tm1mdv:memberSelect` <br> - `Compute`
eventPayload | n/a | The payload body accompanying the message. The content and whether or not this parameter is required depends on the type of message. This must be an object in the following format: `{ k1: v1, k2: v2, ..., kn: vn }` | - `{ name: 'myEventNickname' }` <br> - `{ selectedSet: 'Default' }` <img width=500/>

The following is a list of all the supported messages that the client can send:

Message type | Description | eventPayload
---------- | ------- | ------- |
trigger | Attempts to raise a particular event. The widget may or may not respond, depending on whether or not it is listening for this event name. | Depends on the event. This would contain information needed for the event execution.
execute | Attempts to execute a command on the widget. This is an alias to the `trigger` message type and is provided as a convenience. This is equivalent to sending `type=trigger` and `eventPayload={commandName: <command_name>, commandArgs: { ... }}`. | Depends on the command. This would contain information needed to successfully execute the command.
subscribe | Subscribes to a particular event. Once subscribed, the widget will send an `on` message back to the client every time the `eventName` event occurs. | `name`: String (Required). Used to identify subscriptions. <br> - If the given name has already been registered, nothing happens.
unsubscribe | Unsubscribes to a particular event. Only subscriptions with a name can be unsubscribed. | `name`: String (Required). Used to identify subscriptions.  <br> - If no name is given or if there were no previous subscriptions under that name, nothing happens. <img width=500/>

# Receiving messages

> Examples:

```shell
var widgetReady = false;
window.addEventListener("message", function(event) {
    // only accept messages sent from the UI widget
    if(event.origin == "<production url>") {
        switch(event.data.type) {
            case 'onWidgetLoaded':
                widgetReady = true;
                break;
            case 'on':
                handleEvent(event.data.eventName, event.data.eventPayload);
                break;
            default:
                break;
        }
    }
});
```

> Receiving an `on` message that indicated that the `tm1mdv:executeCommand` event had occured

```shell
function handleEvent(eventName, eventPayload) {
    if(eventName == "tm1mdv:executeCommand") {
        // first subscription was just notified!
        alert(eventPayload.name + ' was just notified!');
    }
}
```
You can find the specifications for the `event` object in the `Window.postMessage` API. 

The following is a list of parameters under the `event.data` object:

Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of message recieved. | - `onWidgetLoaded` <br> - `on`
eventName | Yes | The full name of the event.| - `tm1mdv:executeCommand` <br> - `tm1mdv:redraw` <br> - `tm1mdv:memberSelect`
eventPayload | n/a | The payload body accompanying the message. The contents depends on the type of message. All payloads contain a `name` and `parentStore` properties. |  - `{ name: 'first subscription', parentStore: { ... }, ... }`  <img width=500/>

The following is a list of all the supported messages that the client can send:

Message type | Description | eventPayload
---------- | ------- | ------- |
onWidgetLoaded | Used to notify to the client that the UI widget has been fully rendered and can safely be interacted with. | None 
on | Used to notify to the client that one of their subscribed events has occurred. | This will contain more information about the context of the event that occurred.

# HTML sample 

> 

```html
<html>
	<style>
		.vertical { display: flex; flex-direction: column; }
		.vertical-fill { flex-grow: 2; }
		div#headerContent { margin: 5px; }
		div#headerContent button { margin-left: 20px }
		div#widgetContent iframe#widget { width: 100%; height: 100%; }
	</style>
	<script>
		var API_URL = '<production url>';
		var IS_WIDGET_READY = false;
		var iframe;

		window.onload = function() {
			iframe = document.getElementById('widget');
			window.addEventListener('message', function(evt) {
				// only accept messages sent from the API service
				if(evt.origin == API_URL) {
					switch(evt.data.type) {
						case 'onWidgetLoaded':
							IS_WIDGET_READY = true;
							break;
						case 'on':
							handleEvent(evt.data.eventName, evt.data.eventPayload);
							break;
						default:
							break;
					}
				}
			});
		}

		function handleEvent(name, payload) {
			alert('event "' + name + '" had occurred: ' + JSON.stringify(payload));
		}

		function loadIframe() {
			var url = API_URL + 
						'/ui/' +
						'?type=cube-viewer' +
						'&server=Planning%20Sample' +
						'&cube=plan_BudgetPlan' +
						'&view=Budget%20Input%20Detailed';
			iframe.src = url;
		}
		
		function isWidgetReady() {
			return IS_WIDGET_READY && iframe.contentWindow;
		}
		
		function redraw() {
			if(isWidgetReady()) {
				iframe.contentWindow.postMessage({
					type: 'trigger',
					eventName: 'tm1mdv:redraw'
				}, API_URL);
			}
		}
		
		function swapAxes() {
			if(isWidgetReady()) {
				iframe.contentWindow.postMessage({
					type: 'execute',
					eventName: 'SwapAxes'
				}, API_URL);
			}
		}
		
		function subscribe() {
			if(isWidgetReady()) {
				iframe.contentWindow.postMessage({
					type: 'subscribe',
					eventName: 'tm1mdv:executeCommand',
					eventPayload: {
						name: 'commandsCompleted'
					}
				}, API_URL);
			}
		}
		
		function unsubscribe() {
			if(isWidgetReady()) {
				iframe.contentWindow.postMessage({
					type: 'unsubscribe',
					eventName: 'tm1mdv:executeCommand',
					eventPayload: {
						name: 'commandsCompleted'
					}
				}, API_URL);
			}
		}
	</script>
	<body class="vertical">
		<div id="headerContent">
			<button onclick="loadIframe()">Render iframe</button>
			<button onclick="redraw()">Trigger redraw event</button>
			<button onclick="swapAxes()">Execute swap axis</button>
			<button onclick="subscribe()">Subscribe to "tm1mdv:executeCommand"</button>
			<button onclick="unsubscribe()">Unsubscribe to "tm1mdv:executeCommand"</button>
		</div>
		<div id="widgetContent" class="vertical vertical-fill">
			<iframe id="widget" class="vertical-fill"></iframe>
		</div>
	</body>
</html>
```

This is a sample of an HTML file that uses the IBM Planning Analytics Workspace UI API to create a cube viewer widget in an iframe. In this sample are also full examples of the `trigger`, `execute`, `subscribe`, and `unsubscribe` events.
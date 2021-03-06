Simple STOMP client library in Java.
========

## Description


Library for access to MQ server which supports Stomp Protocol Specification, Version 1.0 (http://stomp.github.com//stomp-specification-1.0.html)

**Update for maven**


##Build
 
 mvn clean install
 




## Example

`````{.java}

package stomp.client.test.async;

import java.net.URI;
import java.net.URISyntaxException;
import stomp.client.Ack;
import stomp.client.StompClient;
import stomp.client.StompException;

public class test {

	public static void main(String[] args) throws StompException, URISyntaxException {
		new test().run(new URI("tcp://login:passcode@localhost:61613"));
	}
	
	public void run(URI uri) throws StompException {
		
		StompClient client = new StompClient(uri) {
			public void onConnected(String sessionId) {
				System.out.println("connected: sessionId = " + sessionId);
			}
			
			public void onDisconnected() {
				System.out.println("disconnected");
			}
			
			public void onMessage(String messageId, String body) {
				System.out.println("message: messageId = " + messageId + " body = " + body);
				try {
					ack(messageId);
				} catch (StompException e) {
				}
			}
			
			public void onReceipt(String receiptId) {
				System.out.println("receipt: receiptId = " + receiptId);
			}
			
			public void onError(String message, String description) {
				System.out.println("error: message = " + message + " description = " + description);
			}
		};
		
		// connect to STOMP server, send CONNECT command and wait CONNECTED answer
		client.connect();
		
		// subscribe on queue
		client.subscribe("/queue/test", Ack.client);
		try {
			Thread.sleep(500);
		} catch (InterruptedException e1) {
		}
		
		
		// send 5 messages
		for(int i=0; i<5; i++) {
			client.send("/queue/test", "message #" + i);
		}
		
		// wait		
		try {
			Thread.sleep(5000);
		} catch (InterruptedException e) {
		}
		
		// unsubscribe
		client.unsubscribe("/queue/test");
		
		// disconnect
		client.disconnect();
		
	}

}

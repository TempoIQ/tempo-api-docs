---
title: Writing Events
---

TempoIQ's HTTP API makes it easy to write events from virtually any language
or framework. The only requirement is to be able to send JSON-formatted
POST requests over HTTPS.

TempoIQ accepts any valid JSON message as an event. In order to write data, you
will need to know your environment's hostname, as well as an API key and secret.
All this information can be found on your environment's Connect IQ page. 

* Contents
{:toc}

## Single Events

**Endpoint**: `POST https://<HOST>/api/channels/0/event`

**Accepts**: JSON object

**Returns**: HTTP Status 202 (Accepted) if body is valid.

**Example:**

    curl -i -u "my-api-key:my-api-secret" \
        'https://acme-inc.pipelines.tempoiq.com/api/channels/0/event' \
        -d '{"voltage": 2.05, "device": "my-test-device"}'

## Bulk Writes

The bulk write endpoint allows you to send an array of events in a 
single request. Events may specify different timestamps from each other,
and any events that omit a timestamp will be timestamped as they are received.

**Endpoint**: `POST https://<HOST>/api/channels/0/event`

**Accepts**: JSON object with `events` array

**Returns**: HTTP Status 202 (Accepted) if body is valid.

**Example:**

    curl -i -u "my-api-key:my-api-secret" \
        'https://acme-inc.pipelines.tempoiq.com/api/channels/0/events' \
        -d '{"events": [ {"voltage": 2.05, "device": "my-test-device"} ] }'

## Language Examples

The following examples illustrate sending an event to TempoIQ over HTTPS in
a variety of languages. These examples have no dependencies
outside each language's standard library. If you are already using a different
HTTP library, it may be worth adapting the example to use that library
to avoid duplicated code.

### iOS - Swift 2.0

{% highlight swift %}

// Set url and loginString based on your environment and credentials
let url = "https://YOUR-HOST.tempoiq.com/api/channels/0/event"
let loginString = "API_KEY:API_SECRET"

// Construct a dictionary to be sent as event data
var eventData = Dictionary<String, AnyObject>()
eventData["device_id"] = "abc123"
eventData["voltage"] = 109.4

// Create request object
let request = NSMutableURLRequest(URL: NSURL(string: url)!)
request.HTTPMethod = "POST"
request.addValue("application/json", forHTTPHeaderField: "Content-Type")

// Construct HTTP Basic Authentication header from our key and secret
let loginData: NSData = loginString.dataUsingEncoding(NSUTF8StringEncoding)!
let base64LoginString = loginData.base64EncodedStringWithOptions(
    NSDataBase64EncodingOptions(rawValue: 0))
request.addValue("Basic \(base64LoginString)", forHTTPHeaderField: "Authorization")

// Convert eventData to a JSON string and set as the request body
do {
    request.HTTPBody = try NSJSONSerialization.dataWithJSONObject(
        eventData as NSDictionary, options: NSJSONWritingOptions(rawValue: 0))
} catch {
    NSLog("Can't serialize Dictionary as JSON!")
}

// Configure request callback
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error -> Void in
    // Handle response
    if error != nil {
        NSLog("HTTP error: %@", error!)
        return
    }
    NSLog("Response code: %@", response!.description)
}
task.resume()   // Finally execute the request

{% endhighlight %}

### Android

{% highlight java %}

/* To write from a UI thread: */
String eventData = "{\"device_id\": \"abc123\", \"voltage\": 3.1415}";
new TempoIQWriterTask().execute(eventData);     // Takes a JSON message serialized as a string

/* Background task to write to TempoIQ */
class TempoIQWriterTask extends AsyncTask<String, Void, Boolean> {
    @Override
    protected Boolean doInBackground(String... events) {
        // Set urlStr and creds based on your environment and credentials
        String urlStr = new URL("https://YOUR-HOST.tempoiq.com/api/channels/0/event");
        String creds = "API_KEY:API_SECRET";

        // Just send the first event
        String eventData = events[0];

        Boolean success = false;
        HttpURLConnection conn = null;
        try {
            URL url = new URL(urlStr);
            conn = (HttpURLConnection) url.openConnection();
            conn.setDoOutput(true);

            // Set request headers
            conn.setRequestMethod("POST");
            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");

            String encodedCreds = Base64.encodeToString(creds.getBytes(), Base64.NO_WRAP);
            System.out.println("creds: " + creds);
            conn.setRequestProperty("Authorization", "Basic " + encodedCreds);

            // Write event data
            OutputStreamWriter wr = new OutputStreamWriter(conn.getOutputStream());
            wr.write(eventData);
            wr.flush();

            // Handle response
            int statusCode = conn.getResponseCode();
            if(statusCode == HttpURLConnection.HTTP_OK || statusCode == HttpURLConnection.HTTP_ACCEPTED){
                System.out.println("Success!");
                success = true;
            }else{
                System.out.println("Connection error: " + statusCode + " " + conn.getResponseMessage());
            }
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (conn != null) {
                conn.disconnect();
            }
        }
        return success;
    }
}

### iOS - objective-c

{% highlight objectivec %}

    // Set url and loginString based on your environment and credentials
    NSString* url = @"https://tempo00.pipelines.tempoiq.com/api/channels/0/event";
    NSString* loginString = @"admin:a60cb87c0b124129bd753d734506a70f";

    // Construct a dictionary to be sent as event data
    NSMutableDictionary<NSString*, NSObject*>* eventData = [[NSMutableDictionary<NSString*, NSObject*> alloc] init];
    [eventData setValue: @"abc123" forKey: @"device_id"];
    [eventData setValue: [NSNumber numberWithDouble: 109.4] forKey: @"voltage"];

    // Create request object
    NSMutableURLRequest* request = [[NSMutableURLRequest alloc] initWithURL: [[NSURL alloc] initWithString: url]];
    [request setHTTPMethod: @"POST"];
    [request addValue:@"application/json" forHTTPHeaderField: @"Content-Type"];


    // Construct HTTP Basic Authentication header from our key and secret
    NSData* loginData = [loginString dataUsingEncoding: NSUTF8StringEncoding];
    NSString* base64LoginString = [loginData base64EncodedStringWithOptions: 0];
    [request addValue: [@"Basic " stringByAppendingString: base64LoginString] forHTTPHeaderField: @"Authorization"];

    // Convert eventData to a JSON string and set as the request body
    @try {
        NSData* body = [NSJSONSerialization dataWithJSONObject: eventData options: 0 error: nil];
        [request setHTTPBody: body];

    } @catch(NSException* exn) {
        NSLog(@"Can't serialize Dictionary as JSON!");
    }

    // Configure request callback
    NSURLSession* session = [NSURLSession sharedSession];
    NSURLSessionDataTask* task = [session dataTaskWithRequest: request];
    [task resume];   // Finally execute the request

{% endhighlight %}


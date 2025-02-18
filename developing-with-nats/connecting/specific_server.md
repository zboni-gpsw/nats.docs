# Connecting to a Specific Server

The NATS client libraries can take a full URL, `nats://demo.nats.io:4222`, to specify a specific server host and port to connect to.

Libraries are removing the requirement for an explicit protocol and may allow `nats://demo.nats.io:4222` or just `demo.nats.io:4222`. Check with your specific client library's documentation to see what URL formats are supported.

For example, to connect to the demo server with a URL you can use:

{% tabs %}
{% tab title="Go" %}
```java
// If connecting to the default port, the URL can be simplified
// to just the hostname/IP.
// That is, the connect below is equivalent to:
// nats.Connect("nats://demo.nats.io:4222")
nc, err := nats.Connect("demo.nats.io")
if err != nil {
	log.Fatal(err)
}
defer nc.Close()

// Do something with the connectioConnection nc = Nats.connect("nats://demo.nats.io:4222");
```
{% endtab %}

{% tab title="Java" %}
```text
Connection nc = Nats.connect("nats://demo.nats.io:4222");

// Do something with the connection

nc.close();
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
let nc = NATS.connect("nats://demo.nats.io:4222");
nc.on('connect', (c) => {
    // Do something with the connection
    doSomething();
    // When done close it
    nc.close();
});
nc.on('error', (err) => {
    failed(err);
});
```
{% endtab %}

{% tab title="Python" %}
```python
nc = NATS()
await nc.connect(servers=["nats://demo.nats.io:4222"])

# Do something with the connection

await nc.close()
```
{% endtab %}

{% tab title="Ruby" %}
```ruby
require 'nats/client'

NATS.start(servers: ["nats://demo.nats.io:4222"]) do |nc|
   # Do something with the connection

   # Close the connection
   nc.close
end
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
// will throw an exception if connection fails
    let nc = await connect("nats://demo.nats.io:4222");
    // Do something with the connection

    // Close the connection
    nc.close();
```
{% endtab %}
{% endtabs %}


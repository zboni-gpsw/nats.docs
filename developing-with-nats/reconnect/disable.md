# Disabling Reconnect

You can disable automatic reconnect with connection options:

{% tabs %}
{% tab title="Go" %}
```go
// Disable reconnect attempts
nc, err := nats.Connect("demo.nats.io", nats.NoReconnect())
if err != nil {
	log.Fatal(err)
}
defer nc.Close()

// Do something with the connection
```
{% endtab %}

{% tab title="Java" %}
```java
Options options = new Options.Builder().
                            server("nats://demo.nats.io:4222").
                            noReconnect(). // Disable reconnect attempts
                            build();
Connection nc = Nats.connect(options);

// Do something with the connection

nc.close();
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
llet nc = NATS.connect({
    reconnect: false,
    servers: ["nats://demo.nats.io:4222"]
});
```
{% endtab %}

{% tab title="Python" %}
```python
nc = NATS()
await nc.connect(
   servers=[
      "nats://demo.nats.io:1222",
      "nats://demo.nats.io:1223",
      "nats://demo.nats.io:1224"
      ],
   allow_reconnect=False,
   )

# Do something with the connection

await nc.close()
```
{% endtab %}

{% tab title="Ruby" %}
```ruby
require 'nats/client'

NATS.start(servers: ["nats://127.0.0.1:1222", "nats://127.0.0.1:1223", "nats://127.0.0.1:1224"], reconnect: false) do |nc|
   # Do something with the connection

   # Close the connection
   nc.close
end
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
// will throw an exception if connection fails
let nc = await connect({
    reconnect: false,
    servers: ["nats://demo.nats.io:4222"]
});
nc.close();
```
{% endtab %}
{% endtabs %}


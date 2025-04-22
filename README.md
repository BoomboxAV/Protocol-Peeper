# Boombox AV Protocol Peeper  
## Wireshark-style Connection Logging within Q-SYS Designer

![main screenshot](screenshots/protocol_peeper.gif)

---

When developing Q-SYS Plugins and Scripts to interact with APIs, having a clear sense of every byte exchanged is critical. The debug output of a Text Controller script can sometimes be a bit cumbersome to read, especially when involving non-printable characters, line breaks, or large chunks of code. Additionally, it's useful to have verbose output of other aspects of the script, unrelated to API calls and responses, which can get intermingled with Tx/Rx printing.

A good solution is to use Wireshark to monitor traffic, but unless filters are set up perfectly, it can output additional unrelated traffic. In certain environments of testing and debugging, such as an AV system on a remote network, having an external method of capturing traffic is difficult.

This is where Protocol Peeper comes into play. It serves as an intermediary between a plugin/script and an API endpoint, and displays a log of TCP traffic in a clear, easy-to-read format.

---

## Instructions

1. Drag the plugin into your design.
2. Set the TCP port to that of the API server being tested. This will typically be the port AV equipment listens to for control.
3. Set the Destination IP to that of the API Server. Traffic **received** by Protocol Peeper from the **client** will be forwarded to the server, and vice-versa.
4. *(Optional)* Set the Destination TCP port. By default, the forwarding destination port will match the above TCP port selected. However, you may modify this by configuring a different TCP port. Since Protocol Peeper binds to all interfaces, this is useful, for example, when both the API server and Protocol Peeper are binding to the loopback interface. This will avoid conflicts in this situation.

---

### How it works

When Protocol Peeper launches and has all necessary configuration, it binds to all interfaces at the specified TCP port. When an incoming connection is established, it starts buffering incoming data and attempts an outgoing connection to the specified address. Once this outgoing connection is established, it forwards all buffered data and begins forwarding data in both directions, printing the data when "Enable Capture" is enabled.

---

## Features

You may pause and resume data capture via the "Enable Capture" button. While paused, you may scroll up and down in the log to see previous entries. While paused, traffic is still forwarded in both directions, however it is not logged or stored — it is discarded after forwarding. You may then resume capturing and data will be logged.

A user pin called Output may be used to externally log data with other scripts.

Any non-printable or non-ASCII bytes are displayed as hex representations.

Incoming data from the API client is left-justified, data from the server is right-justified, errors and information are center-justified.

Filter can be Lua pattern matching or a simple text search.

Open up the debug window for a more copy/paste friendly dump of traffic.

---

## Important info

**There can only be ONE instance of Protocol Peeper in a design.**

Currently, Protocol Peeper only supports TCP connections. Long lines are forcibly wrapped for display. Linebreak characters in the data are printed as HEX representations.

The "client" plugin/script being tested will occasionally display a misleading status. This is because after initialization, Protocol Peeper immediately acts as a server. If Protocol Peeper cannot establish an outbound connection as a client in a timely manner, it will then close the incoming TCP connection. Therefore, depending on the exact method of status reporting of the client plugin, when the API server endpoint is not accepting a connection, timing out, or closes the connection, the client plugin may continuously cycle between Error and OK. This is simply due to the nature of client plugins continuously retrying, and thinking the connection is successful at first, before Protocol Peeper closes the connection due to the server being unreachable.

While this was tested successfully with some AV hardware via TCP and SSH, and some websites via HTTP, it is possible that some handshake / authentication methods fail when Protocol Peeper is in the middle.

---

## Known Issues

Filter may not work for some special characters, for example HTML tags.

There is an issue with multiple consecutive spaces and leading/trailing spaces on a message. May be an EzSVG rendering issue.  Not an issue in the plugin debug window.

---

## Thanks To

A few friendly folks on Communities I reached out to for help testing and feedback!  

Claude AI helped generate much of the SVG rendering, word wrapping, and filtering code, which was then rearranged, fixed, broken, fixed again, broken, and finally fixed again by Andy.

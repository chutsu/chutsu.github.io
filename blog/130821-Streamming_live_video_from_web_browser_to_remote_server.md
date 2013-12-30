# Streaming live video from web browser to remote server

<iframe width="560" height="315" src="http://www.youtube.com/embed/Yie05ZkwVxQ"
frameborder="0" allowfullscreen>
</iframe>

Playing a video in a web browser is relatively easy, sites like Youtube, Vimeo
do that really well already. However performing the reverse is a slightly
different story, current methods use Flash to create a video stream and
transmit that data to a remote server, however in my latest project
[Bowtie](https://github.com/wallarelvo/Bowtie) I attempted to use only HTML5
and Javascript to stream the data to a server, here's how I did it:

- In the webpage you're serving from create a `video` and `convas` html tag.
- Open webcam video stream on the `video` tag
- Draw the video into the `canvas` tag using JavaScript
- Convert video into image slices by using DataURI
- Open web socket to remote server
- Transmit the Base64 encoded string
- Decode the Base64 encoded data back to its intended form (i.e. jpeg)

Example code:

    <video id="live_stream" controls></video>
    <canvas id="vid_img" width="320" height="240" style="display: none;"></canvas>

    <script>
        window.URL = window.URL || window.webkitURL;
        navigator.getUserMedia = navigator.getUserMedia
            || navigator.webkitGetUserMedia
            || navigator.mozGetUserMedia
            || navigator.msGetUserMedia;

        // CONSTANTS
        var CONNECTED_WS = "CONNECTED WS";
        var DISCONNECTED_WS = "DISCONNECTED WS";
        var RETRY_WS = "RETRY WS";
        var DISCONNECT_WS = "DISCONNECT WS";

        // Checks to see if browser supports getUserMedia()
        function hasGetUserMedia() {
            // Note: Opera is unprefixed.
            return !!(navigator.getUserMedia || navigator.webkitGetUserMedia ||
            navigator.mozGetUserMedia || navigator.msGetUserMedia);
        }

        function streamVideo(stream) {
            window.stream = stream;

            if (window.URL) {
                video.src = window.URL.createObjectURL(stream);
            } else {
                video.src = stream;
            }

            video.play();
        }

        function initVideoStream() {
            navigator.getUserMedia(
                media_settings,
                streamVideo,
                function(e) {console.log('Error! Starting video stream:', e)}
            );
        }

        function openWebSocket(url) {
            var ws = null;

            try {
                ws = new WebSocket(url);

                ws.onopen = function () {
                    console.log("Opening websocket connection");
                };

                ws.onmessage = function(message) {
                    console.log("Recieved msg from websocket: [", message.data, "]");
                    if (message.data === DISCONNECT_WS ) {
                        ws.close();
                    }
                }

                ws.onerror = function(error) {
                    if (error.reason != undefined) {
                        console.log("Websocket Error!: " + error.reason);
                    } else {
                        console.log("Websocket Error Detected!");
                    }

                    ws.close();
                    ws = null;
                };

                ws.onclose = function() {
                    ws.close();
                    ws = null;
                }

            } catch (error) {
                console.log("WebScoket Error!: " + error);
            }

            return ws;
        }

        function transmitVideoStreamToURL(url, time_interval) {
            var ws = openWebSocket(url);
            var timer = setInterval(
                function () {
                    // condition that stops transmitting video stream to server
                    if (ws.readyState == 3) { // 3 - websocket is closed
                        console.log("Stop video stream transmission!");
                        clearInterval(timer);
                    } else {
                        // draw video stream to canvas, obtain canvas data as jpg
                        // then transmit to server
                        canvas_context.drawImage(video, 0, 0, 320, 240);
                        var data = canvas.toDataURL('image/jpeg', 1.0);
                        console.log("Transmitting: [" + data + "]");
                        ws.send(data);
                    }
                },
                250
            );

        }

        // variables
        var video = document.getElementById('live_stream');
        var canvas = document.getElementById('vid_img');
        var canvas_context = canvas.getContext('2d');
        var media_settings = {video: true, audio: true};
        var ws_url = "ws://127.0.0.1:8080/websocket/";
        var time_interval = 250;

        // execution pre-checks
        if (hasGetUserMedia()) {
            initVideoStream();
            transmitVideoStreamToURL(ws_url, time_interval);
        } else {
            alert('Error! getUserMedia() is not supported in your browser!');
        }

    </script>

The above is a snippet of how [Bowtie](https://github.com/wallarelvo/Bowtie)
obtains the video stream and transmit it to a remote server. I didn't show you
the server side code, but it should be quite easy to convert the data that is
encoded in Base64 back to its original type (i.e. jpeg).
# Task2
## Programming a web page to control the arms using web series api / writing arudino code related to the controller 





### Here is the HTML code of the web page:

```
<!DOCTYPE html>

<html lang="en" dir="ltr">

<head>

 <meta charset="utf-8">

 <title>speech to text in javascript</title>


 <style>
    *{
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    -ms-box-sizing: border-box;
    box-sizing: border-box;
    }
    body{
    background:linear-gradient(to right , rgb(134, 185, 4), rgb(244, 244, 244) );
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    color: #000;
    font-family: Arial, Helvetica, sans-serif;
    font-size: 16px;
    margin: 0;
            }
    .container {
    text-align: center;
    }
    h1 {
    font-size: 30px;
    color: rgb(6, 4, 4);
    }
    textarea {
    width: 100%;
    height: 200px;
    border-radius: 10px;
    font-size: 20px;
    margin-bottom: 10px;
    }
    button,select{
    padding: 12px 20px ;
    background: rgb(212, 11, 11);
    border: 0px;
    border-radius: 10px;
    cursor: pointer;
    color: rgb(11, 88, 47);
    }

    button:hover,select:hover {
    background: rgb(197, 161, 230);
    color: white;
    }
</style>

 <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/css/bootstrap.min.css">

</head>

<body>

 <div class="container">

 <h1 class="text-center mt-5">

 Speech to Text in JavaScript

 </h1>

 <div class="form-group">

 <textarea  id="textbox" rows="6" class="form-control"></textarea>

 </div>

 <div class="form-group">

 <button id="start-btn"  class="btn btn-danger btn-block" title="" >
    Start
 </button>
<button class = "btn btn-danger btn-block" onclick="onConnectUsb()" id="connect-usb">
    connect
</button>


 
 <p id="instructions">Press the Start button to start speech or connect button to connect to serial port</p>

 </div>

 </div>

 <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>

 <script src="script.js"></script>
<script src="arduino.js"></script>
<script>


</script>
</body>

</html>
```

#### Here is the script:

```
var speechRecognition = window.webkitSpeechRecognition

var recognition = new speechRecognition()

var textbox = $("#textbox")

var instructions = $("#instructions")

var content = ''

recognition.continuous = true


recognition.onstart = function() {

 instructions.text("Voice Recognition is On")

}

recognition.onspeechend = function() {

 instructions.text("No Activity")

}

recognition.onerror = function() {

 instruction.text("Try Again")

}

recognition.onresult = function(event) {

 var current = event.resultIndex;

 var transcript = event.results[current][0].transcript



 content += transcript
 onChangespeech()
 textbox.val(content)

}

$("#start-btn").click(function(event) {

 recognition.start()

})

textbox.on('input', function() {

 content = $(this).val()

})

```

##### Arduino code :

```
let isConnectted = false;
      let port;
      let writer;
      var target_id;
      const enc = new TextEncoder();

      async function onChangespeech() {
        if (!isConnectted) {
          alert("you must connect to the usb in order to use this.");
          return;
        }
       
        try {
          const commandlist = content;
          const commandSplit = commandlist.split(" ")
          const command = commandSplit.slice(-1);
          const computerText = `${command}@`;
          await writer.write(enc.encode(computerText));
        } catch (e) {
          console.log(e);
        }
      }
    
      

    async function onConnectUsb() {
      try {
        const requestOptions = {
          // Filter on devices with the Arduino USB vendor ID.
          filters: [{ usbVendorId: 0x2341 }],
        };

        // Request an Arduino from the user.
        port = await navigator.serial.requestPort(requestOptions);
        await port.open({ baudRate: 115200 });
        writer = port.writable.getWriter();
        isConnectted = true;
      } catch (e) {
        console.log("err", e);
      }
    }
```

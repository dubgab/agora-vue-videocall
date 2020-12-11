<template>
  <div class="mainC">
    <h1 class="titlemain">Probando Video llamadas</h1>
    <p class="titlemain">activos : por definir</p>
    <button v-on:click="join_client">Join</button>

    <div id="app">
      <div class="remote-c">
        <p class="titlemain">Remote video</p>
        <div id="remote-container"></div>
        <div class="btn-controls">
          <button>audio</button>
          <button>video</button>
          <button>exit</button>
        </div>
      </div>

      <div class="container-user">
        <p class="titlemain">Local video</p>
        <div id="me"></div>
        <div class="btn-controls">
          <button>audio</button>
          <button>video</button>
          <button>exit</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AgoraRTC from "agora-rtc-sdk";

// Handle errors.
let handleError = function (err) {
  console.log("Esto no funciona: ");
};

let remoteContainer = document.getElementById("remote-container");

// Remove the video stream from the container.
function removeVideoStream(elementId) {
  let remoteDiv = document.getElementById(elementId);
  if (remoteDiv) remoteDiv.parentNode.removeChild(remoteDiv);
}

export default {
  name: "HelloWorld",
  data: function () {
    return {
      client: 0,
      localStream : null,
    };
  },

  props: {
    channelName: String,
    rolUser: Number,
  },

  mounted() {
    this.client = AgoraRTC.createClient({
      mode: "rtc",
      codec: "vp8",
    });
    
    this.localStream = AgoraRTC.createStream({
      audio: true,
      video: true,
    });

  },

  methods: {
  
  handleError(err) {
    console.log(err)
  },

  join_client() {
    //let response = this.get_token();
    this.client.init("3d2452ae22b54bd7b037d89b006c2cb8")
    let token = "0063d2452ae22b54bd7b037d89b006c2cb8IACc2RBg/20uEmKG3GdydnFc1GoPcVIpwZWrO29YAll6/wx+f9gAAAAAEABC9EaCXdfUXwEAAQBd19Rf"
    this.client.join(
        token,
        this.channelName,
        null,
        (uid) => {
          // Create a local stream
          this.localStream.init(() => {
            // Play the local stream
            this.localStream.play("me");
            // Publish the local stream
            this.client.publish(this.localStream, this.handleError);
          }, this.handleError);
        },
        this.handleError
      );

      let remoteContainer = document.getElementById("remote-container")

      this.client.on("stream-added", function(evt){
        console.log("antes de stream add")
        console.log(evt)
        this.client.subscribe(evt.stream, ()=>{console.log('que paso :(')});
      });


      //Play the remote stream when it is subsribed
      this.client.on("stream-subscribed", function(evt){
        console.log("sucribete")
        console.log(evt)
        let stream = evt.stream;
        let streamId = String(stream.getId());
        this.addVideoStream(streamId);
        stream.play(streamId);
      });



    },

    /*Get token of authentications*/
    async get_token() {
      let url = "http://localhost:8000/streamToken/generate_token/";
      let data = { channel_name: this.channelName, rol_user: this.rolUser };
      let response = await fetch(url, {
        method: "POST", // or 'PUT'
        body: JSON.stringify(data), // data can be `string` or {object}!
        headers: {
          "Content-Type": "application/json",
        },
      })
        .then((res) => res.json())
        .catch((error) => console.error("Error:", error))
        .then((response) => response);
      return response;
    },

    /*Get token of authentications*/
    addVideoStream(elementId) {
      console.log("-----------------llamada-----------------------");
      // Creates a new div for every stream
      let streamDiv = document.createElement("div");
      // Assigns the elementId to the div.
      streamDiv.id = elementId;
      // Takes care of the lateral inversion
      streamDiv.style.transform = "rotateY(180deg)";
      // Adds the div to the container.
      remoteContainer.appendChild(streamDiv);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#app {
  display: flex;
  flex-direction: row;
  margin: 10px 5%;
  width: 90%;
  border: 1px solid #000;
  height: 90vh;
  position: relative;
}

.titlemain {
  text-align: center;
}

#me {
  width: 350px;
  height: 200px;
  background-color: aqua;
}

#remote-container {
  width: 350px;
  height: 200px;
  margin: 5px;
  background-color: aqua;
}

.remote-c {
  position: relative;
}

.btn-controls {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
</style>

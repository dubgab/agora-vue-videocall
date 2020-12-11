<template>
  <div class="Inicio">
      <div class="form-container" v-if="form_pass == true">
          <input type="text" name="channel_name" id="channels_name" placeholder="channel_name"  v-model="channel_name">
          <input type="text" name="rol_user" id="rol_user" placeholder="rol_user" v-model="rol_user">
          <button @click="onForm">Iniciar</button>
      </div>

      <hello-world v-else :channelName="channel_name" :rolUser="rol_user"> </hello-world>

  </div>
</template>

<script>
import AgoraRTC from "agora-rtc-sdk";
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  data: function () {
    return {
      channel_name : "",
      rol_user : 1,
      form_pass : true
    };
  },
  components: {
    HelloWorld,
  },methods:{
    onForm(){
      this.form_pass = false
    }
  }
};
</script>

<style>

</style>

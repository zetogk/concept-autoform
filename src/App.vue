<template>
  <div id="app">
    <h4>Insert your yaml definition</h4>
    <textarea v-model="inputDefinition"></textarea>
    <br/> <!-- {{dynamicDefinition}} --> <hr>
    <AutoForm v-bind:definition="dynamicDefinition"/>
  </div>
</template>

<script>
import AutoForm from './components/AutoForm.vue'
import yaml from 'js-yaml';


export default {
  name: 'app',
  data: function(){
    return {
      inputDefinition: `
        props:
        - name: name
          type: string
        - name: lastname
          type: string
      `,
      definition: ``
      //definition: "{\"props\":[{\"name\":\"credentials\",\"type\":\"object\",\"props\":[{\"name\":\"authIsRequired\",\"type\":\"decision\",\"options\":[{\"value\":\"true\",\"props\":[{\"name\":\"key\",\"type\":\"string\",\"required\":true},{\"name\":\"keyName\",\"type\":\"string\",\"required\":true},{\"name\":\"val\",\"type\":\"string\",\"required\":true},{\"name\":\"valName\",\"type\":\"string\",\"required\":true},{\"name\":\"endpointAuth\",\"type\":\"string\",\"required\":true,\"validation\":[\"url\"]},{\"name\":\"method\",\"type\":\"enum\",\"required\":true,\"default\":\"POST\",\"values\":[\"GET\",\"POST\",\"PUT\",\"PATCH\"]},{\"name\":\"pathResponse\",\"type\":\"string\",\"required\":true}]},{\"value\":\"false\"}]}]},{\"name\":\"httpRequest\",\"type\":\"object\",\"props\":[{\"name\":\"endpoint\",\"type\":\"string\",\"required\":true,\"validation\":[\"url\"]},{\"name\":\"method\",\"type\":\"enum\",\"default\":\"GET\",\"values\":[\"GET\",\"POST\",\"PUT\",\"PATCH\"]},{\"name\":\"responsePath\",\"type\":\"string\",\"required\":true}]},{\"name\":\"customDefinition\",\"type\":\"object\",\"props\":[{\"name\":\"authIsRequired\",\"type\":\"decision\",\"required\":true,\"options\":[{\"value\":\"false\"},{\"value\":\"true\",\"props\":[{\"name\":\"authType\",\"type\":\"decision\",\"required\":true,\"options\":[{\"value\":\"basicauth\",\"props\":[{\"name\":\"basicHeaderName\",\"type\":\"string\",\"required\":true},{\"name\":\"basicIncludeBasic\",\"type\":\"bool\",\"default\":true,\"required\":true}]},{\"value\":\"token\",\"props\":[{\"name\":\"tokenLocationRequest\",\"type\":\"decision\",\"options\":[{\"value\":\"header\",\"props\":[{\"name\":\"tokenHeaderName\",\"type\":\"string\",\"required\":true},{\"name\":\"tokenIncludeBearer\",\"type\":\"bool\",\"default\":false,\"required\":true}]},{\"value\":\"payload\",\"props\":[{\"name\":\"tokenPayloadPath\",\"type\":\"string\",\"required\":true}]}]}]},{\"value\":\"apiKey\"},{\"value\":\"jwt\",\"props\":[{\"name\":\"tokenLocationRequest\",\"type\":\"decision\",\"options\":[{\"value\":\"header\",\"props\":[{\"name\":\"tokenHeaderName\",\"type\":\"string\",\"required\":true},{\"name\":\"tokenIncludeBearer\",\"type\":\"bool\",\"default\":false,\"required\":true}]},{\"value\":\"payload\",\"props\":[{\"name\":\"tokenPayloadPath\",\"type\":\"string\",\"required\":true}]}]}]}]}]}]}]}]}"
    }
  },
  computed: {
    dynamicDefinition: function() {
      try {
        const jsDefinition = yaml.safeLoad(this.inputDefinition);
        console.log('dynamicDefinition updated to: ', JSON.stringify(jsDefinition))
        return JSON.stringify(jsDefinition);
      } catch (err) {
        console.log('Error dynamicDefinition: ', err.message);
        return "";
      }
      this.$forceUpdate();
    }
  },
  components: {
    AutoForm
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

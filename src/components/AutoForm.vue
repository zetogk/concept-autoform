<template>
  <div class="autoform">
    <h1>AutoForm plugin</h1>
    <!-- Definition: {{definition}} -->

    <div v-if="error.isThere">
      {{error.message}}
    </div>
    <div v-if="!error.isThere" class="fieldsform">
        <div v-for="(e, index) in elements" :key="index" class="field">
            
            <div v-if="e.type === 'section'">
                <h3>{{e.label}}</h3>
            </div>
            
            <!-- {{e.name}} : {{e.fromDecision}} || {{e.dependency.path}} || {{e.dependency.value}} || {{data[e.dependency.path]}} || {{e.dependency.value == data[e.dependency.path]}} -->
            
            <div v-if="e.type === 'input' && (!e.fromDecision || (e.fromDecision && data[e.dependency.path] == e.dependency.value))">
                <label>{{e.label}}</label>
                <input v-model="data[e.link]" :required="e.required">
            </div>

            <div v-if="e.type === 'select' && (!e.fromDecision || (e.fromDecision && data[e.dependency.path] == e.dependency.value))">
                <label>{{e.label}}</label>
                <select v-model="data[e.link]" >
                    <option v-for="(v, i) in e.options" :key="i" :value="v">{{v}}</option>
                </select>
            </div>

        </div>
    </div>

    <div style="display:none"> {{trick}} </div>

    <pre>
      {{data}}
    </pre>

    <!-- <pre>
      {{elements}}
    </pre> -->

  </div>
</template>

<script>

import dotProp from 'dot-prop'

export default {
  name: 'AutoForm',
  computed: {
    trick: function(){
      console.log('this.definition: ', this.definition)
      return this.definition;
    }
  },
  methods: {
    checkExistsElement(elementToCheck) {
      return this.elements.some(el => {
        return el.name === elementToCheck.name && el.dependency.path === elementToCheck.dependency.path && el.link === elementToCheck.link
      })
    },
    initRender() {
      try {
        this.currentDefinition = this.definition;
        this.definitionDin = JSON.parse(this.definition);
        this.error = {
          isThere: false,
          message: ''
        }
      } catch (err) {
        console.log(err.message)
        this.error = {
          isThere: true,
          message: err.message
        }
      }
      if ('props' in this.definitionDin && !this.error.isThere) {
          this.evaluateProps(this.definitionDin.props)
      }
    },
    evaluateProps(props, path = '', fromDecision = false, decisionPath = '', valueDependency = '') {
      const len = props.length;
      let newElement = null
      for (let i = 0; i < len; i++) {

          const element = props[i];

          switch (element.type) {
              case 'object':

                this.elements.push({
                  name: element.name,
                  label: element.name,
                  type: 'section',
                  dependency: {
                    path: null,
                    value: null
                  },
                })

                if ('props' in element) {
                  //this.data[element.name] = {}
                  this.evaluateProps(element.props, path == '' ? element.name : `${path}.${element.name}`)
                }
                
                break;

              case 'bool':

                newElement = {
                  name: element.name,
                  label: element.name,
                  type: 'select',
                  link: `${path}.${element.name}`,
                  options: ["false", "true"],
                  fromDecision: fromDecision,
                  dependency: {
                    path: decisionPath,
                    value: valueDependency
                  },
                };

                if (!this.checkExistsElement(newElement)) {
                  this.elements.push(newElement);
                }
                
                break;
              
              case 'enum':

                newElement = {
                  name: element.name,
                  label: element.name,
                  type: 'select',
                  link: `${path}.${element.name}`,
                  options: element.values,
                  fromDecision: fromDecision,
                  dependency: {
                    path: decisionPath,
                    value: valueDependency
                  },
                };
                
                if (!this.checkExistsElement(newElement)) {
                  this.elements.push(newElement);
                }

                break;

                case 'decision':

                  newElement = {
                  name: element.name,
                  label: element.name,
                  type: 'select',
                  link: `${path}.${element.name}`,
                  options: element.options.map(option => {
                    return option.value
                  }),
                  fromDecision: fromDecision,
                  dependency: {
                    path: decisionPath,
                    value: valueDependency
                  },
                };
                  
                if (!this.checkExistsElement(newElement)) {
                  this.elements.push(newElement);
                }

                const lenAux = element.options.length;
                for (let j = 0; j < lenAux; j++){
                  const option = element.options[j];
                  if ('props' in option) {
                    this.evaluateProps(option.props, path, true, `${path}.${element.name}`, option.value)
                  }
                }
                break;

              case 'string':

                newElement = {
                  name: element.name,
                  label: element.name,
                  type: 'input',
                  link: `${path}.${element.name}`,
                  fromDecision: fromDecision,
                  dependency: {
                    path: decisionPath,
                    value: valueDependency
                  },
                  required: element.required || false
                }

                if (!this.checkExistsElement(newElement)) {
                  this.elements.push(newElement);
                }

                break;
          
              default:
                break;
          }
      }
    } // END evaluateProps 
  },
  data: function(){
      return {
        currentDefinition: '',
        error: {
          isThere: false,
          message: ''
        },
        definitionDin: {},
        data: {},
        elements: []
      }
  },
  props: {
    definition: String
  },
  created: function(){
      
      this.initRender();
      
  },
  beforeUpdate: function(){
    /* console.log('beforeUpdate')
    console.log(':::definition: ', this.definition) */
  },
  updated: function (){
    if (this.currentDefinition != this.definition) {
      this.elements = [];
      this.data = {};
      this.initRender();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.autoform {
    border: 1px solid red;
}

.autoform label {
    display: block;
}

</style>

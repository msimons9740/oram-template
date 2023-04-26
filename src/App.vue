<template>
  <div class="container mb-5">
    <template v-for="component in componentList" :key="`comp-${component.name}`">
      <div class="row mt-5">
        <div class="col-12">
          <h1 class="display-4">{{ component.name }}</h1>
          <h3>{{ component.description }}</h3>
        </div>
      </div>
      <template v-for="(instance, instanceIndex) in component.instances" :key="`instance-${instanceIndex}`">
        <div class="row mt-4">
          <p>
            <span class="badge rounded-pill text-bg-success">{{ instanceIndex + 1 }}</span>
            {{ instance.description }}
          </p>
        </div>

        <div class="row">
          <div class="col b0-light-mode p-4">
            <component :is="component.component" v-bind="instance.props" v-model="instance.value" />
          </div>
          <div class="col b0-dark-mode p-4" data-bs-theme="dark">
            <component :is="component.component" v-bind="instance.props" />
          </div>
        </div>

        <div class="row mt-3" v-show="component.showValue">
          <div class="col b0-light-mode p-4">
            <span class="badge rounded-pill text-bg-dark float-end">Value</span>
            <pre class="component-code">{{ instance.value }}</pre>
          </div>
        </div>

        <div class="row mt-4">
          <div class="col b0-light-mode p-4">
            <span class="badge rounded-pill text-bg-dark float-end">Usage</span>
            <pre class="component-code">{{ generateImplementationCode(component.tag, instance.props) }}</pre>
          </div>
        </div>
      </template>
    </template>
</div>
</template>

<script lang="ts">
import { markRaw, defineComponent } from 'vue'
import MyComponent from '@/components/MyComponent.vue'
import SimpleButton from '@/components/SimpleButton.vue'
import RandomQuote from '@/components/RandomQuote.vue'
import UserFilter from '@/components/UserFilter.vue'
import { demoUserList1 } from '@/data/users'

type instanceDescription = {
  description: string,
  props: {
    [ key: string ]: string | Array<object>
  },
  value?: any,
}

export default defineComponent({
  name: 'App',

  data() {
    return {
      componentList: [
        {
          name: 'My component',
          description: 'This is my component\'s description',
          component: markRaw(MyComponent),
          tag: 'my-component',
          instances: [
            {
              description: 'My component with default props',
              props: { }
            },
            {
              description: 'My component with a different message',
              props: {
                msg: 'A different text'
              }
            },
          ] as instanceDescription[]
        },
        {
          name: 'Button',
          description: 'A simple button',
          component: markRaw(SimpleButton),
          tag: 'simple-button',
          instances: [
            {
              description: 'Simple instance with default props',
              props: {
              }
            },
            {
              description: 'Another instance of the same button but with the type set to "primary"',
              props: {
                caption: 'Test button',
                type: 'primary',
              }
            },
          ] as instanceDescription[]
        },
        {
          name: 'RandomQuote',
          description: 'A random quote generator',
          component: markRaw(RandomQuote),
          tag: 'random-quote',
          showValue: true,
          instances: [
            {
              description: 'Random quote instance with default props',
              props: { },
              value: [] as string[],
            },
            {
              description: 'Random quote instance with a maximum of three quotes',
              props: {
                caption: 'Max 3 quotes',
                maxQuotes: 3,
              },
              value: [] as string[],
            },
          ] as instanceDescription[]
        },
        {
          name: 'User filter',
          description: 'Simple user filtering component',
          component: markRaw(UserFilter),
          tag: 'user-filter',
          showValue: true,
          instances: [
            {
              description: 'User filter with default props',
              props: {
                list: demoUserList1,
              },
              value: [],
            },
            {
              description: 'User filter with different caption and pre-selected list',
              props: {
                caption: 'Select Users',
                type: 'primary',
                list: demoUserList1,
              },
              value: [
                {
                  id: 'u1'
                },
              ],
            },
          ] as instanceDescription[]
        },
      ]
    }
  },

  methods: {
    generateImplementationCode(tag: string, props: any) {
      let s = `<${tag}`
      Object.keys(props).forEach((k) => {
        if (k == 'value') {
          return
        }

        if (typeof props[k] === 'boolean') {
          s += `\n  :${k}="${props[k]}"`
          return
        }

        if (typeof props[k] === 'object') {
          s += `\n  :${k}="${JSON.stringify(props[k])}"`
          return
        }

        s += `\n  ${k}="${props[k]}"`
      })
      s += `></${tag}>`

      return s
    },
  }
})
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

.b0-light-mode {
  background-color: #f8f8f8;
  border-radius: 5px;
}

.b0-dark-mode {
  color: #eee;
  background-color: #212529;
  border-radius: 5px;
}

.component-code {
  margin: 0;
  font-size: .7em;
}
</style>

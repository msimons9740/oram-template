<template>
  <div>
    <simple-button type="primary" :caption="caption" @click="generateQuote()" />

    <ul class="list-group mt-3" v-show="quotes.length">
      <li v-for="(quote, index) in quotes" :key="index" class="list-group-item d-flex justify-content-between align-items-center">
        {{ quote }}
        <a class="text-danger" href="javascript:;" @click="quotes.splice(index, 1)"><i class="bi bi-trash"></i></a>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import SimpleButton from '@/components/SimpleButton.vue'

export default defineComponent({
  components: {
    SimpleButton
  },

  props: {
    caption: {
      type: String,
      default: 'Generate quote',
    },
    maxQuotes: {
      type: Number,
      default: 0,
    },
  },

  data() {
    return {
      quotes: [] as string[],
      quoteList: [
        'I used to think I was indecisive, but now I\'m not sure.',
        'I\'m not arguing, I\'m just explaining why I\'m right.',
        'I have a photographic memory, but I always forget to put the lens cap back on.',
        'I\'m not superstitious, but I am a little stitious.',
        'I told my wife she was drawing her eyebrows too high. She looked surprised.',
        'I\'m not lazy, I\'m just on energy-saving mode.',
        'I love deadlines. I like the whooshing sound they make as they fly by.',
        'I\'m not sure what my spirit animal is, but I\'m pretty sure it has a drinking problem.',
      ],
    }
  },

  methods: {
    generateQuote() {
      const quoteIndex = Math.floor(Math.random() * this.quoteList.length)
      this.quotes.push(this.quoteList[ quoteIndex ])

      if (this.maxQuotes && this.quotes.length > this.maxQuotes) {
        this.quotes.shift()
      }

      this.$emit('update:modelValue', this.quotes)
    }
  },
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>

<template>
  <div class="dropdown user-filter">
    <slot>
      <button :class="`btn btn-${type} dropdown-toggle`" type="button" data-bs-toggle="dropdown" aria-expanded="false">
        <i class="bi bi-people"></i>
        {{ caption }}
      </button>
    </slot>
    <div class="dropdown-menu p-2">
      <input type="email" class="form-control" v-model="searchQuery" :placeholder="searchPlaceholder">

      <div class="user-container">
        <div v-for="item in filteredList" :key="`user-${item.payload.email}`" @click.stop="item.selected = ! item.selected" class="user-entry rounded d-flex align-items-center justify-content-start p-1 mt-2">
          <div class="me-2">
            <input type="checkbox" v-model="item.selected" />
          </div>
          <div class="me-2">
            <img v-if="item.payload.image" :src="item.payload.image" class="rounded-circle" />
            <div v-else class="user-placeholder d-flex align-items-center justify-content-center">
              <i class="bi bi-person"></i>
            </div>
          </div>
          <div>
            <h2>{{ item.payload.name }}</h2>
            <p>{{ item.payload.email }}</p>
          </div>
        </div>
      </div>

      <button class="btn btn-sm btn-secondary mt-2 w-100" type="button" @click="selectUsers">
        {{ caption }}
        <span v-show="selectedCount">({{ selectedCount }})</span>
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue'
import { User } from '@/types/user.type'

type innerListItem = {
  payload: User,
  selected: boolean,
}

export default defineComponent({
  props: {
    modelValue: {
      type: Array as PropType<Array<User>>,
    },
    caption: {
      type: String,
      default() { return 'Assign' }
    },
    searchPlaceholder: {
      type: String,
      default() { return 'Search User...' }
    },
    list: {
      type: Array as PropType<Array<User>>,
      default() { return [] }
    },
    type: {
      type: String,
      default: 'light',
    }
  },

  data() {
    return {
      searchQuery: '',
      innerList: [] as innerListItem[],
    }
  },

  created() {
    this.list.forEach(payload => {
      this.innerList.push({
        payload,
        selected: this.modelValue?.filter(u => u.id == payload.id).length ? true : false,
      })
    })
  },

  methods: {
    selectUsers() {
      this.$emit('update:modelValue', this.innerList.filter(item => item.selected).map(item => item.payload))
    }
  },

  computed: {
    filteredList(): innerListItem[] {
      const searchQuery = this.searchQuery.toLocaleLowerCase()

      // filter by search query
      const filtered = this.innerList.filter(item => item.payload.name.toLocaleLowerCase().includes(searchQuery) || item.payload.email.toLocaleLowerCase().includes(searchQuery))

      // display the selected items at the top
      return filtered.sort((a, b) => a.selected && ! b.selected ? -1 : 1)
    },

    selectedCount(): number {
      return this.innerList.filter(item => item.selected).length
    }
  }
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.user-filter {
  --image-width: 60px;
  .user-container {
    max-height: 40vh;
    overflow-y: auto;
    .user-entry {
      cursor: pointer;
      .user-placeholder {
        width: var(--image-width);
        height: var(--image-width);
        background-color: var(--bs-tertiary-bg);
        border-radius: var(--image-width);
        i.bi {
          font-size: 2em;
        }
      }
      &:hover {
        background-color: var(--bs-secondary-bg);
      }
      img {
        width: var(--image-width);
      }
      h2 {
        margin: 0;
        font-size: 1.05em;
      }
      p {
        margin: 0;
        font-size: 0.9em;
      }
    }
  }
}
</style>

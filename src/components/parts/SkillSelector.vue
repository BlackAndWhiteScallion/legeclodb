<template>
  <b-popover :target="target" triggers="click blur" :delay="{show:0, hide:250}" no-fade placement="bottom">
    <div class="chr-area">
      <b-link v-if="nullable"  @mouseover="$emit('mouseover', null)" @mouseleave="$emit('mouseleave', null)" @click="onClick(null)">
        <b-img-lazy :src="getImageURL('null')" title="(なし)" width="50" height="50" class="rounded-bordered" />
      </b-link>
      <b-link v-for="(v, i) in filteredItems" :key="i" @mouseover="$emit('mouseover', v)" @mouseleave="$emit('mouseleave', v)" @click="onClick(v)">
        <b-img-lazy :src="getImageURL(v.icon)" :title="`${descToTitle(v)}`" width="50" />
      </b-link>
    </div>
  </b-popover>
</template>

<script>
import common from "../common";

export default {
  name: 'SkillSelector',
  mixins: [common],
  props: {
    target: {
      type: String,
      required: true,
    },
    skills: {
      type: Array,
      required: true,
    },
    excludes: {
      type: Array,
      default: () => [],
    },
    characters: {
      type: Array,
      default: () => [],
    },

    nullable: {
      type: Boolean,
      default: false,
    },
    closeonclick: {
      type: Boolean,
      default: false,
    },
    chrSelector: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      currentChr: null,
    }
  },

  created() {
  },

  methods: {
    onClick(item) {
      this.$emit('click', item);
      if (this.closeonclick) {
        this.$root.$emit('bv::hide::popover', this.target)
      }
    }
  },

  computed: {
    filteredItems() {
      return this.skills.filter(a => !this.excludes.includes(a));
    },
  },
}
</script>

<style>
.chr-area {
  flex-wrap: wrap;
  align-items: flex-start;
  align-content: flex-start;
  width: 425px;
  min-height: 150px;
  max-height: 250px;
  overflow-y: auto;
  overflow-x: hidden;
}

/*
.btn-outline-secondary {
  padding: 3px !important;
}
*/
</style>

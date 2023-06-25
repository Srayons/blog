<template>
  <div v-show="chooseEmoji" class="emoji-wrapper">
    <span
        class="emoji-item"
        v-for="(value, key, index) of emojiList"
        :key="index"
        @click="addEmoji(key)"
    >
      <span class="emoji" v-html="value"/>
    </span>
  </div>
</template>

<script>
import EmojiList from "../assets/js/emoji";

export default {
  props: {
    chooseEmoji: {
      type: Boolean
    }
  },
  data: function () {
    return {
      emojiList: EmojiList
    };
  },
  methods: {
    addEmoji(key) {
      this.$emit("addEmoji", key);
    }
  }
};
</script>

<style scoped>
.emoji {
  user-select: none;
  margin: 0.25rem;
  display: inline-block;
  vertical-align: middle;
}

.emoji-item {
  cursor: pointer;
  display: inline-block;
  font-size: 1.575rem;
}

.emoji-item:hover {
  transition: all 0.2s;
  border-radius: 0.25rem;
  background: #dddddd;
}

.emoji-wrapper {
  max-height: 150px;
  overflow-y: auto;
}
</style>

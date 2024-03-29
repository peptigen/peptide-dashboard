<template>
  <div>
    <h1 class="is-size-4 is-spaced bd-anchor-title">Enter Sequence (N to C)</h1>
    <div class="field has-addons">
      <div class="control is-expanded">
        <input
          id="sequence"
          class="input"
          spellcheck="false"
          autocorrect="off"
          type="text"
          placeholder="Peptide Sequence"
          v-model="internalSequence"
          autofocus
          @keydown="onKeyDown"
          @keyup="lastKeyCode = 0"
          @keyup.enter="finishSequence"
        />
      </div>
      <div class="control">
        <a
          class="button is-info"
          :class="{ 'is-loading': !ready && sequence.length > 0 }"
          @click="finishSequence"
        >
          Save
        </a>
      </div>
    </div>
    <p id="seq-link" v-if="sequence.length > 0" class="help is-pulled-right">
      Permanent Link:
      <a :href="'https://peptide.bio?s=' + sequence" target="_blank">{{
        "peptide.bio?s=" + sequence
      }}</a>
    </p>
    <br />
  </div>
</template>

<script>
// https://stackoverflow.com/questions/46289311/vue-limit-characters-in-text-area-input-truncate-filter
export default {
  name: "SequenceInput",
  props: {
    ready: false,
  },
  data() {
    return {
      sequence: "",
      pattern: "acdefghiklmnpqrstvwyACDEFGHIKLMNPQRSTVWY",
      lastKeyCode: 0,
    };
  },
  mounted: function () {
    // convert pattern to list of integers
    this.pattern = this.pattern.split("").map((x) => {
      return x.charCodeAt(0);
    });
    const queryParam = new URLSearchParams(window.location.search).get("s");
    if (queryParam) {
      // clean it up
      this.internalSequence = queryParam.replace(
        /[^acdefghiklmnpqrstvwyACDEFGHIKLMNPQRSTVWY]/g,
        ""
      );
    }
  },
  computed: {
    internalSequence: {
      get: function () {
        return this.sequence;
      },
      set: function (v) {
        this.sequence = v.toUpperCase();
        this.$emit("sequence-update", v.toUpperCase());
      },
    },
  },
  methods: {
    onKeyDown: function (evt) {
      // do this instead of rex so it's faster
      // check if it's a control character
      if (evt.keyCode >= 48 && evt.keyCode <= 90) {
        // check for ctrl, so we don't eat hot keys
        if (this.lastKeyCode !== 17 && this.pattern.indexOf(evt.keyCode) < 0)
          evt.preventDefault();
      } else if (evt.keyCode >= 186 || evt.keyCode === 32) {
        // punctuation and space
        evt.preventDefault();
      }
      this.lastKeyCode = evt.keyCode;
    },
    finishSequence: function () {
      this.$emit("sequence-push");
    },
  },
};
</script>

<style lang="scss">
#sequence {
  text-transform: uppercase;
}

#seq-link {
  max-height: 1rem;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  max-width: 100%;
}
</style>

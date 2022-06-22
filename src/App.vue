<template>
  <div id="io">
    <label for="input">Input</label>
    <textarea
      data-gramm="false"
      data-gramm_editor="false"
      data-enable-grammarly="false"
      spellcheck="false"
      autocapitalize="false"
      autocomplete="false"
      id="input"
      v-model="input"
    ></textarea>
    <br />
    <label for="output">Output</label>
    <textarea readonly id="output" v-model="output"></textarea>
  </div>
  <div id="actions">
    <button id="deflate" @click="deflate">Deflate</button>
    <button id="inflate" @click="inflate">Inflate</button>
    <div>
      <input type="checkbox" id="raw" v-model="raw" />
      <label for="raw">Raw</label>
    </div>
    <button id="copy" @click="copy">{{ copyText }}</button>
  </div>
</template>

<script>
import pako from 'pako';

export default {
  name: 'App',
  data() {
    return {
      raw: false,
      input: '',
      output: '',
      copyText: 'Copy Result',
    };
  },
  methods: {
    deflate() {
      try {
        this.output = pako.deflate(this.input, { raw: this.raw });
      } catch (e) {
        alert('Error thrown: ' + e);
        console.error(e);
      }
    },
    inflate() {
      try {
        this.output = pako.inflate(new Uint8Array(this.input.split(',')), { to: 'string', raw: this.raw });
      } catch (e) {
        alert('Error thrown: ' + e);
        console.error(e);
      }
    },
    copy() {
      let that = this;
      navigator.permissions.query({ name: 'clipboard-write' }).then(function (permissionStatus) {
        if (permissionStatus.state === 'granted' || permissionStatus.state === 'prompt') {
          navigator.clipboard.writeText(that.output);
          that.copyText = 'Copied!';
          setTimeout(function () {
            that.copyText = 'Copy Result';
          }, 5000);
        } else {
          alert('Access was denied to clipboard-write, please give access to continue.');
        }
      });
    },
  },
};
</script>

<style>
@import './styles/index.css';

textarea {
  resize: none;
  width: 100%;
  height: 30%;
  margin-top: 5px;
}

label {
  text-align: left;
  width: 100%;
}

#io {
  width: 70%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100%;
  flex-direction: column;
}

#actions {
  width: 25%;
  margin-left: 5%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100%;
  flex-direction: column;
  background: #5581b533;
  border-radius: 10px;
}

#actions > * {
  width: 60%;
  margin: 5px 0;
  user-select: none;
  -webkit-user-select: none;
}

#actions * {
  font-size: 1.25rem;
  cursor: pointer;
}

#actions > button {
  background-color: #30a75044;
  border-radius: 12px;
  height: 2.5rem;
  box-shadow: 0px 2px 1px 2px black;
  border: 2px solid #fabb05aa;
  outline: none;
}

#actions > button:active {
  box-shadow: 0px 1px 0px 1px black;
}
</style>

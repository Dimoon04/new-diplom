<template>
  <div v-once class="component-barcode-scanner">
    <svg class="dce-bg-loading" viewBox="0 0 1792 1792">
      <path
        d="M1760 896q0 176-68.5 336t-184 275.5-275.5 184-336 68.5-336-68.5-275.5-184-184-275.5-68.5-336q0-213 97-398.5t265-305.5 374-151v228q-221 45-366.5 221t-145.5 406q0 130 51 248.5t136.5 204 204 136.5 248.5 51 248.5-51 204-136.5 136.5-204 51-248.5q0-230-145.5-406t-366.5-221v-228q206 31 374 151t265 305.5 97 398.5z">
      </path>
    </svg>
    <svg class="dce-bg-camera" viewBox="0 0 2048 1792">
      <path
        d="M1024 672q119 0 203.5 84.5t84.5 203.5-84.5 203.5-203.5 84.5-203.5-84.5-84.5-203.5 84.5-203.5 203.5-84.5zm704-416q106 0 181 75t75 181v896q0 106-75 181t-181 75h-1408q-106 0-181-75t-75-181v-896q0-106 75-181t181-75h224l51-136q19-49 69.5-84.5t103.5-35.5h512q53 0 103.5 35.5t69.5 84.5l51 136h224zm-704 1152q185 0 316.5-131.5t131.5-316.5-131.5-316.5-316.5-131.5-316.5 131.5-131.5 316.5 131.5 316.5 316.5 131.5z">
      </path>
    </svg>
    <div class="dce-video-container"></div>
    <!-- <div class="dce-scanarea">
      <div class="dce-scanlight"></div>
    </div> -->
  
  </div>
</template>

<script>
import { BarcodeScanner } from "dynamsoft-javascript-barcode";

export default {
  data() {
    return {
      pScanner: null,
    };
  },
  async mounted() {
    try {
      const scanner = await (this.pScanner = BarcodeScanner.createInstance());
      await scanner.setUIElement(this.$el);
      scanner.onFrameRead = (results) => {
        for (let result of results) {
          console.log(result.barcodeText);
        }
      };
      scanner.onUniqueRead = (txt, result) => {
        alert(txt, result);
      };
      await scanner.open();
    } catch (ex) {
      let errMsg;
      if (ex.message?.includes("network connection error")) {
        errMsg = "Failed to connect to Dynamsoft License Server: network connection error. Check your Internet connection or contact Dynamsoft Support (support@dynamsoft.com) to acquire an offline license.";
      } else {
        errMsg = ex.message||ex;
      }
      console.error(errMsg);
      alert(errMsg);
    }
  },
  async beforeDestroy() {
    if (this.pScanner) {
      (await this.pScanner).destroyContext();
      console.log("BarcodeScanner Component Unmount");
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.component-barcode-scanner {
  width: 100%;
  height: 100%;
  /* min-width: 640px; */
  min-height: 480px;
  background: #eee;
  position: relative;
  resize: both;
}

.dce-bg-loading {
  display: none;
  animation: 1s linear infinite dce-rotate;
  width: 40%;
  height: 40%;
  position: absolute;
  margin: auto;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  fill: #aaa;
}

.dce-bg-camera {
  display: none;
  width: 40%;
  height: 40%;
  position: absolute;
  margin: auto;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  fill: #aaa;
}

.dce-video-container {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
}

.dce-scanarea {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}

.dce-scanlight {
  display: none;
  width: 100%;
  height: 3%;
  position: absolute;
  animation: 3s infinite dce-scanlight;
  border-radius: 50%;
  box-shadow: 0px 0px 2vw 1px #00e5ff;
  background: #fff;
}

.div-select-container {
  position: absolute;
  left: 0;
  top: 0;
}

.dce-sel-camera {
  display: block;
}

.dce-sel-resolution {
  display: block;
  margin-top: 5px;
}

.dbr-msg-poweredby {
  position: absolute;
  left: 50%;
  bottom: 10%;
  transform: translateX(-50%);
}

.dbr-msg-poweredby svg {
  height: max(3vmin, 17px);
  fill: #ffffff;
}

@keyframes dce-rotate {
  from {
    transform: rotate(0turn);
  }

  to {
    transform: rotate(1turn);
  }
}

@keyframes dce-scanlight {
  from {
    top: 0;
  }

  to {
    top: 97%;
  }
}
</style>
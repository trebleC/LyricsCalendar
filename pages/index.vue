<template>
  <el-row type="flex" class="poster-container" justify="center">
    <el-col :span="12">
      <div
        id="poster-preview"
        :style="{ cursor: imageUrl ? 'move' : 'default' }"
        @mousedown.prevent="mouseDown($event)"
        @mousemove.prevent="mouseMove($event)"
        @mouseup.prevent="mouseUp($event)"
      >
        <img
          class="author-img"
          v-if="imageUrl"
          :src="imageUrl"
          ref="avatar"
          :style="{
            width: avatarPos.width + 'vh',
            height: avatarPos.height + 'vh',
            left: avatarPos.left + 'vh',
            top: avatarPos.top + 'vh',
          }"
        />
        <img
          class="poster-template"
          :src="`poster-template${imageUrl?'-nosign':''}.png`"
        />
        <div class="poster-content">
          <div
            class="title"
            :style="{ 'font-size': 3.7 * titleFontSize + 'vh','color':fontColors[fontColorIdx]}"
            v-html="'“ ' + title + ' ”'"
          ></div>
          <div class="name" :style="{ 'font-size': 3.7 * nameFontSize + 'vh','color':fontColors[fontColorIdx] }">
            {{ name }}
          </div>
          <div
            class="topic"
            :style="{ 'font-size': 2.3 * topicFontSize + 'vh','color':fontColors[fontColorIdx] }"
          >
            {{ topic }}{{ topic && singer ? "_" : "" }}{{ singer }}
          </div>
          <div class="time">
            <span>{{ melodist ? "作曲_" + melodist : "" }}</span>
            <span>{{ time }}</span>
          </div>
        </div>
      </div>
    </el-col>
    <el-col
      :span="8"
      class="poster-control"
      v-loading="isDownloading"
      element-loading-text="生成海报中"
    >
      <el-row>
        <h1>#撻著一句廣東歌歌詞 2021 海报生成器</h1>
        <!-- <el-radio-group size="small" v-model="lang">
          <el-radio-button label="zh"></el-radio-button>
          <el-radio-button label="en"></el-radio-button>
        </el-radio-group> -->
      </el-row>
      <el-form>
        <!-- <el-form-item label="论坛名称" id="track">
          <el-autocomplete
            v-model="track"
            :fetch-suggestions="trackQuery"
          ></el-autocomplete>
        </el-form-item> -->
        <el-form-item label="来自哪首歌?">
          <!-- <el-checkbox v-model="isKeynote">主题演讲</el-checkbox> -->
          <el-autocomplete
            style="width: 100%"
            v-model="topic"
            :fetch-suggestions="songQuery"
            @select="songSelect"
          ></el-autocomplete>
          
        </el-form-item>
        <el-form-item label="歌词">
          <span class="lyric-desc" @click="addBr">换行</span>
          <el-input type="textarea" v-model="title" />
        </el-form-item>
        <el-form-item label="填词" style="margin-top: 2vw">
          <el-autocomplete
            style="width: 100%"
            v-model="name"
            :fetch-suggestions="nameQuery"
            @select="nameSelect"
          ></el-autocomplete>
        </el-form-item>
        <el-form-item label="作曲">
          <el-input v-model="melodist" />
        </el-form-item>
        <el-form-item label="歌手">
          <el-input v-model="singer" />
        </el-form-item>
        <el-form-item label="日期">
          <el-input v-model="time" />
        </el-form-item>
        <el-form-item
          :label="'特别符号' + (imageUrl ? '（可在海报中拖动）' : '')"
        >
          <el-col :span="3">
            <input
              type="file"
              ref="avatarInput"
              style="display: none"
              @change="avatarChange"
            />
            <a class="avatar-uploader" href="javascript:;" @click="setAvatar()">
              <i class="el-icon-plus avatar-icon"></i>
            </a>
          </el-col>
          <el-col :span="9" class="icon-panel">
            <a href="javascript:;" @click="zoomIn()">
              <i class="el-icon-zoom-in avatar-icon" v-if="imageUrl"></i>
            </a>
            <a href="javascript:;" @click="zoomOut()">
              <i class="el-icon-zoom-out avatar-icon" v-if="imageUrl"></i>
            </a>
            <a href="javascript:;" @click="zoomReset()">
              <i class="el-icon-refresh-left avatar-icon" v-if="imageUrl"></i>
            </a>
            <a href="javascript:;" @click="zoomDestroy()">
              <i class="el-icon-circle-close avatar-icon" v-if="imageUrl"></i>
            </a>
          </el-col>
        </el-form-item>
        <!-- <el-form-item label="发布时间">
          <el-input v-model="time" />
        </el-form-item> -->
        <el-form-item label="字号调整（歌词）">
          <el-col  :span="8">
            <a href="javascript:;" @click="titleFontAdd()">
              <i class="el-icon-zoom-in avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="titleFontMinus()">
              <i class="el-icon-zoom-out avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="titleFontReset()">
              <i class="el-icon-refresh-left avatar-icon"></i>
            </a>
          </el-col >
          <el-col  :span="8">
            <label class="el-form-item__label">字体颜色</label>
            <a href="javascript:;" @click="fontColorReset()">
              <i class="el-icon-refresh avatar-icon"></i>
            </a>
            <el-color-picker style="position:relative;top:5px;" v-if="selectColor" v-model="selectColor" @change="changeFontColor" size="mini"></el-color-picker>
          </el-col >
        </el-form-item>
        <el-form-item label="字号调整（填词）">
          <div :span="12">
            <a href="javascript:;" @click="nameFontAdd()">
              <i class="el-icon-zoom-in avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="nameFontMinus()">
              <i class="el-icon-zoom-out avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="nameFontReset()">
              <i class="el-icon-refresh-left avatar-icon"></i>
            </a>
          </div>
        </el-form-item>
        <el-form-item label="字号调整（歌名）">
          <div :span="12">
            <a href="javascript:;" @click="topicFontAdd()">
              <i class="el-icon-zoom-in avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="topicFontMinus()">
              <i class="el-icon-zoom-out avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="topicFontReset()">
              <i class="el-icon-refresh-left avatar-icon"></i>
            </a>
          </div>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="download()"> 生成海报 </el-button>
        </el-form-item>

        <el-form-item class="info">
          <i class="el-icon-loading"></i> 多谢靓女<a
            href="http://github.com/Ovilia"
            >@Ovilia</a
          >的模板 设计版权<a href="https://weibo.com/u/6675339465"
            >@撻著廣東歌</a
          >所有 <a href="mailto:oviliazhang@gmail.com">问题反馈</a>
        </el-form-item>
        <el-form-item class="info"> 支持广东歌就系支持紧你自己！ </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<script lang="ts">
import Vue from "vue";
// @ts-ignore
import domtoimage from "retina-dom-to-image";
import trackZhRaw from "./data/track-zh";
import trackEnRaw from "./data/track-en";

type SpeechInfo = {
  track: string;
  title: string;
  name: string;
  topic: string;
  time: string;
};

function getTrackInfo(raw: string, isZh: boolean): SpeechInfo[] {
  let infos = raw
    .split("\n")
    .slice(2)
    .filter((line) => !!line)
    .map((line) => {
      const arr = line.split(",");
      return {
        track: arr[2],
        title: arr[4],
        name: arr[1],
        topic: arr[3],
        time: arr[5],
      };
    });


  return infos;
}

const trackZh = getTrackInfo(trackZhRaw, true);
const trackEn = getTrackInfo(trackEnRaw, false);

export default Vue.extend({
  data() {
    return {
      track: "",
      imageUrl: null as unknown as string,
      title: "",
      name: "",
      topic: "",
      time: "",
      melodist: "",
      singer: "",
      avatarInput: null,

      titleFontSize: 1,
      nameFontSize: 1,
      topicFontSize: 1,

      fontColors:['#ffe342','#38809C'],
      fontColorIdx:0,
      selectColor:'',

      avatarDefaultPos: {
        width: 0,
        height: 0,
        top: 0,
        left: 0,
      },
      avatarPos: {
        width: 0,
        height: 0,
        top: 0,
        left: 0,
      },
      avatarZoom: 1,
      isMouseDown: false,
      mouseX: 0,
      mouseY: 0,
      isDownloading: false,
      posterBase64: "",

      lang: "zh",
    };
  },

  mounted() {
    this.time = this.timeFormat('yyyy.MM.dd')
  },

  methods: {
    trackQuery(str: string, cb: Function) {
      
      const trackInfos = this.lang === "zh" ? trackZh : trackEn;
      const result = str
        ? trackInfos.filter((info) => info.track.includes(str))
        : trackInfos;
      const distinct = result
        .map((info) => info.track)
        .filter((track, index, self) => self.indexOf(track) === index);
      cb(
        distinct.map((track) => {
          console.log(track)
          return { value: track };
        })
      );
    },
    songQuery(str: string, cb: Function) {
      const trackInfos = this.lang === "zh" ? trackZh : trackEn;
      const result = str
        ? trackInfos.filter((info) => info.name === str)
        : trackInfos.filter((info) => info.track === this.track);
      cb(
        result.map((track) => {
          return {
            value: track.name,
            track,
          };
        })
      );
    },

    songSelect(item: { track: SpeechInfo; value: string }) {
      this.topic = item.track.topic;
      this.time = item.track.time;
      this.title = item.track.title;
      this.nameFontReset();
      this.topicFontReset();
    },

    nameQuery(str: string, cb: Function) {
      const trackInfos = this.lang === "zh" ? trackZh : trackEn;
      const result = str
        ? trackInfos.filter((info) => info.name === str)
        : trackInfos.filter((info) => info.track === this.track);
      cb(
        result.map((track) => {
          return {
            value: track.name,
            track,
          };
        })
      );
    },

    nameSelect(item: { track: SpeechInfo; value: string }) {
      this.topic = item.track.topic;
      this.time = item.track.time;
      this.title = item.track.title;
      this.nameFontReset();
      this.topicFontReset();
    },

    download() {
      this.isDownloading = true;
      domtoimage
        .toJpeg(document.getElementById("poster-preview"))
        .then((url: string) => {
          this.posterBase64 = url;
          this.isDownloading = false;
          const link = document.createElement("a");
          link.href = url;
          link.download = this.topic + ".jpeg";
          link.click();
        });
    },

    mouseDown(event: MouseEvent) {
      if (!this.imageUrl) {
        return;
      }
      this.isMouseDown = true;
      this.mouseX = event.pageX;
      this.mouseY = event.pageY;
    },

    mouseMove(event: MouseEvent) {
      if (!this.isMouseDown) {
        return;
      }
      const ratio = 100 / window.innerHeight;

      this.avatarPos.left += (event.pageX - this.mouseX) * ratio;
      this.avatarPos.top += (event.pageY - this.mouseY) * ratio;
      this.mouseX = event.pageX;
      this.mouseY = event.pageY;
    },

    mouseUp(event: MouseEvent) {
      this.isMouseDown = false;
    },

    zoomIn() {
      this.avatarZoom += 0.05;
      this.avatarPos.width = this.avatarDefaultPos.width * this.avatarZoom;
      this.avatarPos.height = this.avatarDefaultPos.height * this.avatarZoom;
    },

    zoomOut() {
      this.avatarZoom -= 0.05;
      this.avatarPos.width = this.avatarDefaultPos.width * this.avatarZoom;
      this.avatarPos.height = this.avatarDefaultPos.height * this.avatarZoom;
    },

    zoomReset() {
      this.avatarZoom = 1;
      this.avatarPos.width = this.avatarDefaultPos.width;
      this.avatarPos.height = this.avatarDefaultPos.height;
      this.avatarPos.left = this.avatarDefaultPos.left;
      this.avatarPos.top = this.avatarDefaultPos.top;
    },

    zoomDestroy(){
      this.imageUrl = ''
    },

    titleFontAdd() {
      this.titleFontSize += 0.1;
    },

    titleFontMinus() {
      this.titleFontSize -= 0.1;
    },

    titleFontReset() {
      this.titleFontSize = 1;
    },
    
    fontColorReset() {
      console.log(this.fontColorIdx)
      this.fontColorIdx ++
      this.fontColorIdx = this.fontColorIdx > this.fontColors.length-1? 0 :this.fontColorIdx
      this.selectColor = this.fontColors[this.fontColorIdx]
    },

    changeFontColor(val:any){
      this.fontColors.push(val)
      this.fontColorIdx = this.fontColors.length-1
      this.selectColor = val
    },

    nameFontAdd() {
      this.nameFontSize += 0.1;
    },

    nameFontMinus() {
      this.nameFontSize -= 0.1;
    },

    nameFontReset() {
      this.nameFontSize = 1;
    },

    topicFontAdd() {
      this.topicFontSize += 0.1;
    },

    topicFontMinus() {
      this.topicFontSize -= 0.1;
    },

    topicFontReset() {
      this.topicFontSize = 1;
    },

    setAvatar() {
      if (this.$refs.avatarInput) {
        (this.$refs.avatarInput as HTMLElement).click();
      }
    },
    addBr(){
      this.title += '<br>'
    },
    // @ts-ignore
    timeFormat(fmt:any) {
        // 对Date的扩展，将 Date 转化为指定格式的String
        // 月(M)、日(d)、小时(h)、分(m)、秒(s)、季度(q) 可以用 1-2 个占位符， 
        // 年(y)可以用 1-4 个占位符，毫秒(S)只能用 1 个占位符(是 1-3 位的数字) 
        // 例子： 
        // (new Date()).Format("yyyy-MM-dd hh:mm:ss.S") ==> 2006-07-02 08:09:04.423 
        // (new Date()).Format("yyyy-M-d h:m:s.S")      ==> 2006-7-2 8:9:4.18 
        let date = new Date()
        var o:any = {
            "M+": date.getMonth() + 1, //月份 
            "d+": date.getDate(), //日 
            "h+": date.getHours(), //小时 
            "m+": date.getMinutes(), //分 
            "s+": date.getSeconds(), //秒 
            "q+": Math.floor((date.getMonth() + 3) / 3), //季度 
            "S": date.getMilliseconds() //毫秒 
        };
        if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
        for (var k in o)
        if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
        return fmt;
    },

    // @ts-ignore
    avatarChange(event) {
      const file =
        event.target.files && event.target.files.length
          ? event.target.files[0]
          : null;
      if (file) {
        this.imageUrl = window.URL.createObjectURL(file);

        const img = new Image();
        img.onload = () => {
          const h = (417 * 100) / 2208;
          const w = (h / img.height) * img.width;
          const top = (1200 / 2208) * 100;
          const pw = (60 / 2208) * 1242;
          const left = (pw - w) / 2;
          this.avatarDefaultPos.width = this.avatarPos.width = w;
          this.avatarDefaultPos.height = this.avatarPos.height = h;
          this.avatarDefaultPos.left = this.avatarPos.left = left;
          this.avatarDefaultPos.top = this.avatarPos.top = top;
        };
        img.src = this.imageUrl;
      }
    },
  },
});
</script>

<style>
@font-face {
  font-family: "Open Sans";
  font-style: normal;
  font-weight: bold;
  src: url(~assets/OpenSans-Bold.ttf) format("truetype");
}
@font-face {
  font-family: "Open Sans";
  font-style: normal;
  font-weight: normal;
  src: url(~assets/OpenSans-Light.ttf) format("truetype");
}
@font-face {
  font-family: "SourceHanSerifSC";
  font-style: normal;
  font-weight: 300;
  src: url(~assets/SourceHanSerifSC-Heavy.otf) format("opentype");
}

h1 {
  margin-bottom: 15px;
  font-size: 20px;
  font-family: "SourceHanSerifSC", "Open Sans";
}

.poster-container {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  font-family: "Open Sans";
}

#poster-preview {
  position: relative;
  width: max-content;
  height: 100vh;
  overflow: hidden;
  -webkit-user-select: none;
  user-select: none;
}

.poster-template {
  height: 100vh;
}

.poster-content {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  padding: 12vh 3vh 0 3vh;
  text-align: center;
  color: #fff;
  font-size: 2vh;
}

.author-img {
  position: absolute;
  z-index: 99;
}

.title {
  font-weight: normal;
  color: #ffe342;
  height: 36vh;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: "SourceHanSerifSC", "Open Sans";
}

.name {
  margin: 4vh 5vh 0.5vh 0;
  color: #ffe342;
  font-family: "SourceHanSerifSC", "Open Sans";
  font-size: 3.7vh;
  height: 29vh;
  font-weight: bold;
  text-align: right;
}

.topic {
  padding-right: 5vh;
  font-size: 2.3vh;
  font-weight: bold;
  text-align: right;
  height: 4.5vh;
  margin-top: 3vh;
  color: #ffe342;
  font-family: "SourceHanSerifSC", "Open Sans";
}

.time {
  font-size: 2vh;
  color: #82666C;
  margin-top: 1vh;
  display: flex;
  justify-content: space-between;
  padding: 0 5vh 0 6vh;
  box-sizing: border-box;
}

.el-form-item {
  margin-bottom: 5px;
}

#track {
  margin-top: 10px;
}

.avatar-uploader {
  display: inline-block;
  margin-top: 10px;
  width: 40px;
  height: 40px;
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader:hover {
  border-color: #409eff;
}

.avatar-icon {
  font-size: 22px;
  color: #ccc;
  width: 40px;
  height: 40px;
  line-height: 40px;
  text-align: center;
}

.icon-panel {
  margin-top: 12px;
}

.el-upload:hover .avatar-icon,
a:hover .avatar-icon {
  color: #409eff;
}

.avatar {
  width: 40px;
  height: 40px;
  display: block;
}

.info,
.info a {
  color: #aaa;
}
.lyric-desc{
  font-size: 12px;
  color: #aaa;
  cursor: pointer;
}
</style>

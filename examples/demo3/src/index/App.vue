<template>
  <div>
    <div class="group" v-for="item in list" :key="item">
      <div class="label">{{item}}</div>
      <div class="comp">
        <div v-if="item === 'normal'">
          <div>
            <div class="inline">hello </div>
            <div class="inline">world!</div>
          </div>
          <div>
            <a class="margin-left-10 block" href="javascript: void(0)">fake jump</a>
          </div>
        </div>
        <!-- 可使用 html 标签替代的内置组件 -->
        <div v-else-if="item === 'img'">
          <img src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg" width="50" height="50" @load="onImgLoad" />
          <img src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg" mode="top" width="50" height="50" @load="onImgLoad" />
        </div>
        <div v-else-if="item === 'input'">
          <input type="text" placeholder="请输入文本内容" @input="onInput" v-model="input.inputText" />
          <input type="number" placeholder="请输入数字内容" @input="onInput" v-model="input.inputNumber" data-is-number="yes" />
          <input type="radio" />
          <input type="radio" name="radio" value="radio1" @input="onInput" v-model="input.inputRadio" />
          <input type="radio" name="radio" value="radio2" @input="onInput" v-model="input.inputRadio" />
          <input type="checkbox" @input="onInput" v-model="input.inputCheckbox" />
        </div>
        <textarea v-else-if="item === 'textarea'" placeholder="请输入内容" maxlength="50" :auto-height="true" value="我是 textarea" @input="onTextareaInput" />
        <div v-else-if="item === 'label'">
          <!-- input -->
          <label>
            <div>输入框1</div>
            <input placeholder="输入框1" @change="onLabelChange"/>
          </label>
          <label for="input2">
            <div>输入框2</div>
          </label>
          <input id="input2" placeholder="输入框2" @change="onLabelChange"/>
          <!-- radio -->
          <label>
            <div>radio1</div>
            <input name="label-radio" type="radio" checked="checked" @change="onLabelChange"/>
          </label>
          <label for="input3">
            <div>radio2</div>
          </label>
          <input name="label-radio" type="radio" id="input3" @change="onLabelChange"/>
          <!-- checkbox -->
          <label>
            <div>checkbox1</div>
            <input type="checkbox" @change="onLabelChange"/>
          </label>
          <label for="input4">
            <div>checkbox2</div>
          </label>
          <input type="checkbox" id="input4" @change="onLabelChange"/>
          <!-- switch -->
          <label>
            <div>switch1</div>
            <template>
              <wx-component v-if="!wxPrefix" behavior="switch" @change="onLabelChange"></wx-component>
              <wx-switch v-else @change="onLabelChange"></wx-switch>
            </template>
          </label>
          <label for="switch2">
            <div>switch2</div>
          </label>
          <template>
            <wx-component v-if="!wxPrefix" behavior="switch" id="switch2" @change="onLabelChange"></wx-component>
            <wx-switch v-else id="switch2" @change="onLabelChange"></wx-switch>
          </template>
        </div>
        <video v-else-if="item === 'video'" class="video" src="http://wxsnsdy.tc.qq.com/105/20210/snsdyvideodownload?filekey=30280201010421301f0201690402534804102ca905ce620b1241b726bc41dcff44e00204012882540400&bizid=1023&hy=SH&fileparam=302c020101042530230204136ffd93020457e3c4ff02024ef202031e8d7f02030f42400204045a320a0201000400" :muted="true" :show-mute-btn="true" :controls="true">
          <Inner></Inner>
        </video>
        <canvas v-else-if="item === 'canvas'" class="canvas" ref="canvas" canvas-id="canvas" width="300" height="200">
          <Inner style="margin-top: 100px;"></Inner>
        </canvas>
        <!-- 使用 wx-component 来创建内置组件 -->
        <template v-else-if="item === 'view'">
          <wx-component v-if="!wxPrefix" :behavior="item">我是视图</wx-component>
          <wx-view v-else-if="wxPrefix === 1">我是视图</wx-view>
          <view v-else-if="wxPrefix === 2">我是视图</view>
          <wx-component v-if="!wxPrefix" :behavior="item" :hidden="true">我是 hidden 视图</wx-component>
          <wx-view v-else-if="wxPrefix === 1" :hidden="true">我是 hidden 视图</wx-view>
          <view v-else-if="wxPrefix === 2" :hidden="true">我是 hidden 视图</view>
        </template>
        <template v-else-if="item === 'text'">
          <wx-component v-if="!wxPrefix" :behavior="item" :selectable="true">{{'this is first line\nthis is second line'}}</wx-component>
          <wx-text v-else-if="wxPrefix === 1" :selectable="true">{{'this is first line\nthis is second line'}}</wx-text>
          <text v-else-if="wxPrefix === 2" :selectable="true">{{'this is first line\nthis is second line'}}</text>
        </template>
        <template v-else-if="item === 'button'">
          <wx-component v-if="!wxPrefix" :behavior="item" open-type="share">分享</wx-component>
          <wx-button v-else-if="wxPrefix === 1" open-type="share">分享</wx-button>
          <button v-else-if="wxPrefix === 2" open-type="share">分享</button>
        </template>
        <template v-else-if="item === 'image'">
          <wx-component v-if="!wxPrefix" :behavior="item" src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg"></wx-component>
          <wx-image v-else-if="wxPrefix === 1" src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg"></wx-image>
          <image v-else-if="wxPrefix === 2" src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg"></image>
        </template>
        <template v-else-if="item === 'icon'">
          <div v-if="!wxPrefix">
            <div><wx-component :behavior="item" v-for="subItem in icon.size" :key="subItem" type="success" :size="subItem"></wx-component></div>
            <div><wx-component :behavior="item" v-for="subItem in icon.color" :key="subItem" type="success" size="40" :color="subItem"></wx-component></div>
            <div><wx-component :behavior="item" v-for="subItem in icon.type" :key="subItem" :type="subItem" size="40"></wx-component></div>
          </div>
          <div v-else-if="wxPrefix === 1">
            <div><wx-icon v-for="subItem in icon.size" :key="subItem" type="success" :size="subItem"></wx-icon></div>
            <div><wx-icon v-for="subItem in icon.color" :key="subItem" type="success" size="40" :color="subItem"></wx-icon></div>
            <div><wx-icon v-for="subItem in icon.type" :key="subItem" :type="subItem" size="40"></wx-icon></div>
          </div>
          <div v-else-if="wxPrefix === 2">
            <div><icon v-for="subItem in icon.size" :key="subItem" type="success" :size="subItem"></icon></div>
            <div><icon v-for="subItem in icon.color" :key="subItem" type="success" size="40" :color="subItem"></icon></div>
            <div><icon v-for="subItem in icon.type" :key="subItem" :type="subItem" size="40"></icon></div>
          </div>
        </template>
        <template v-else-if="item === 'progress'">
          <div v-if="!wxPrefix">
            <wx-component :behavior="item" percent="20" :show-info="true"></wx-component>
            <wx-component :behavior="item" percent="40" stroke-width="12"></wx-component>
            <wx-component :behavior="item" percent="60" color="pink"></wx-component>
            <wx-component :behavior="item" percent="80" :active="true"></wx-component>
          </div>
          <div v-else-if="wxPrefix === 1">
            <wx-progress percent="20" :show-info="true"></wx-progress>
            <wx-progress percent="40" stroke-width="12"></wx-progress>
            <wx-progress percent="60" color="pink"></wx-progress>
            <wx-progress percent="80" :active="true"></wx-progress>
          </div>
          <div v-else-if="wxPrefix === 2">
            <progress percent="20" :show-info="true"></progress>
            <progress percent="40" stroke-width="12"></progress>
            <progress percent="60" color="pink"></progress>
            <progress percent="80" :active="true"></progress>
          </div>
        </template>
        <template v-else-if="item === 'navigator'">
          <wx-component v-if="!wxPrefix" :behavior="item" target="miniProgram" open-type="exit">退出小程序</wx-component>
          <wx-navigator v-else-if="wxPrefix === 1" target="miniProgram" open-type="exit">退出小程序</wx-navigator>
          <navigator v-else-if="wxPrefix === 2" target="miniProgram" open-type="exit">退出小程序</navigator>
        </template>
        <template v-else-if="item === 'open-data'">
          <div v-if="!wxPrefix">
            <wx-component :behavior="item" type="userNickName"></wx-component>
            <wx-component :behavior="item" type="userAvatarUrl"></wx-component>
            <wx-component :behavior="item" type="userGender"></wx-component>
            <wx-component :behavior="item" type="userGender" lang="zh_CN"></wx-component>
            <wx-component :behavior="item" type="userCity"></wx-component>
            <wx-component :behavior="item" type="userProvince"></wx-component>
            <wx-component :behavior="item" type="userCountry"></wx-component>
            <wx-component :behavior="item" type="userLanguage"></wx-component>
          </div>
          <div v-else-if="wxPrefix === 1">
            <wx-open-data type="userNickName"></wx-open-data>
            <wx-open-data type="userAvatarUrl"></wx-open-data>
            <wx-open-data type="userGender"></wx-open-data>
            <wx-open-data type="userGender" lang="zh_CN"></wx-open-data>
            <wx-open-data type="userCity"></wx-open-data>
            <wx-open-data type="userProvince"></wx-open-data>
            <wx-open-data type="userCountry"></wx-open-data>
            <wx-open-data type="userLanguage"></wx-open-data>
          </div>
          <div v-else-if="wxPrefix === 2">
            <open-data type="userNickName"></open-data>
            <open-data type="userAvatarUrl"></open-data>
            <open-data type="userGender"></open-data>
            <open-data type="userGender" lang="zh_CN"></open-data>
            <open-data type="userCity"></open-data>
            <open-data type="userProvince"></open-data>
            <open-data type="userCountry"></open-data>
            <open-data type="userLanguage"></open-data>
          </div>
        </template>
        <template v-else-if="item === 'picker'">
          <div v-if="!wxPrefix">
            <wx-component :behavior="item" :value="1" :range="['美国', '中国', '巴西', '日本']">点击&nbsp;&nbsp;选择国家</wx-component>
            <wx-component :behavior="item" mode="region" @change="onPickerChange">
              <span>点击&nbsp;&nbsp;</span>
              <span>选择城市</span>
            </wx-component>
          </div>
          <div v-else-if="wxPrefix === 1">
            <wx-picker :value="1" :range="['美国', '中国', '巴西', '日本']">点击&nbsp;&nbsp;选择国家</wx-picker>
            <wx-picker mode="region" @change="onPickerChange">
              <span>点击&nbsp;&nbsp;</span>
              <span>选择城市</span>
            </wx-picker>
          </div>
          <div v-else-if="wxPrefix === 2">
            <picker :value="1" :range="['美国', '中国', '巴西', '日本']">点击&nbsp;&nbsp;选择国家</picker>
            <picker mode="region" @change="onPickerChange">
              <span>点击&nbsp;&nbsp;</span>
              <span>选择城市</span>
            </picker>
          </div>
        </template>
        <template v-else-if="item === 'switch'">
          <div v-if="!wxPrefix">
            <wx-component :behavior="item" type="switch" :checked="true" @change="onSwitchChange"></wx-component>
            <wx-component :behavior="item" type="checkbox" @change="onSwitchChange"></wx-component>
          </div>
          <div v-else-if="wxPrefix === 1">
            <wx-switch type="switch" :checked="true" @change="onSwitchChange"></wx-switch>
            <wx-switch type="checkbox" @change="onSwitchChange"></wx-switch>
          </div>
          <div v-else-if="wxPrefix === 2">
            <switch type="switch" :checked="true" @change="onSwitchChange"></switch>
            <switch type="checkbox" @change="onSwitchChange"></switch>
          </div>
        </template>
        <template v-else-if="item === 'slider'">
          <wx-component v-if="!wxPrefix" :behavior="item" min="50" max="200" :show-value="true" @change="onSliderChange"></wx-component>
          <wx-slider v-else-if="wxPrefix === 1" min="50" max="200" :show-value="true" @change="onSliderChange"></wx-slider>
          <slider v-else-if="wxPrefix === 2" min="50" max="200" :show-value="true" @change="onSliderChange"></slider>
        </template>
        <template v-else-if="item === 'map'">
          <wx-component v-if="!wxPrefix" :behavior="item" :class="item" :longitude="113.324520" :latitude="23.099994" :scale="14" :controls="map.controls" :markers="map.markers" :polyline="map.polyline" :show-location="true" @markertap="onMapMarkerTap" @regionchange="onMapRegionChange" @controltap="onMapControlTap">
            <Inner></Inner>
          </wx-component>
          <wx-map v-else-if="wxPrefix === 1" :class="item" :longitude="113.324520" :latitude="23.099994" :scale="14" :controls="map.controls" :markers="map.markers" :polyline="map.polyline" :show-location="true" @markertap="onMapMarkerTap" @regionchange="onMapRegionChange" @controltap="onMapControlTap">
            <Inner></Inner>
          </wx-map>
          <map v-else-if="wxPrefix === 2" :class="item" :longitude="113.324520" :latitude="23.099994" :scale="14" :controls="map.controls" :markers="map.markers" :polyline="map.polyline" :show-location="true" @markertap="onMapMarkerTap" @regionchange="onMapRegionChange" @controltap="onMapControlTap">
            <Inner></Inner>
          </map>
        </template>
        <template v-else-if="item === 'cover-view'">
          <wx-compoennt v-if="!wxPrefix" :behavior="item">测试 cover-view</wx-compoennt>
          <wx-cover-view v-else-if="wxPrefix === 1">测试 cover-view</wx-cover-view>
          <cover-view v-else-if="wxPrefix === 2">测试 cover-view</cover-view>
        </template>
        <template v-else-if="item === 'cover-image'">
          <wx-component v-if="!wxPrefix" behavior="cover-view">
            <wx-component :behavior="item" src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg"></wx-component>
          </wx-component>
          <wx-cover-view v-else-if="wxPrefix === 1">
            <wx-cover-image src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg"></wx-cover-image>
          </wx-cover-view>
          <cover-view v-else-if="wxPrefix === 2">
            <wx-cover-image src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg"></wx-cover-image>
          </cover-view>
        </template>
        <template v-else-if="item === 'live-player'">
          <wx-component v-if="!wxPrefix" :behavior="item" :class="item" mode="live" :autoplay="true" src="rtmp://live.hkstv.hk.lxdns.com/live/hks" @statechange="onLivePlayerStateChange">
            <Inner></Inner>
          </wx-component>
          <wx-live-player v-else-if="wxPrefix === 1" :class="item" mode="live" :autoplay="true" src="rtmp://live.hkstv.hk.lxdns.com/live/hks" @statechange="onLivePlayerStateChange">
            <Inner></Inner>
          </wx-live-player>
          <live-player v-else-if="wxPrefix === 2" :class="item" mode="live" :autoplay="true" src="rtmp://live.hkstv.hk.lxdns.com/live/hks" @statechange="onLivePlayerStateChange">
            <Inner></Inner>
          </live-player>
        </template>
        <template v-else-if="item === 'live-pusher'">
          <wx-component v-if="!wxPrefix" :behavior="item" :class="item" mode="RTC" :autopush="true" url="https://domain/push_stream" @statechange="onLivePusherStateChange">
            <Inner></Inner>
          </wx-component>
          <wx-live-pusher v-else-if="wxPrefix === 1" :class="item" mode="RTC" :autopush="true" url="https://domain/push_stream" @statechange="onLivePusherStateChange">
            <Inner></Inner>
          </wx-live-pusher>
          <live-pusher v-else-if="wxPrefix === 2" :class="item" mode="RTC" :autopush="true" url="https://domain/push_stream" @statechange="onLivePusherStateChange">
            <Inner></Inner>
          </live-pusher>
        </template>
        <template v-else-if="item === 'editor'">
          <wx-component v-if="!wxPrefix" :behavior="item" placeholder="请输入内容" :show-img-size="true" :show-img-toolbar="true" :show-img-resize="true" @statuschange="onEditorStatusChange" @ready="onEditorReady"></wx-component>
          <wx-editor v-else-if="wxPrefix === 1" placeholder="请输入内容" :show-img-size="true" :show-img-toolbar="true" :show-img-resize="true" @statuschange="onEditorStatusChange" @ready="onEditorReady"></wx-editor>
          <editor v-else-if="wxPrefix === 2" placeholder="请输入内容" :show-img-size="true" :show-img-toolbar="true" :show-img-resize="true" @statuschange="onEditorStatusChange" @ready="onEditorReady"></editor>
        </template>
        <template v-else-if="item === 'camera'">
          <wx-component v-if="!wxPrefix" :behavior="item" :class="item">
            <Inner></Inner>
          </wx-component>
          <wx-camera v-else-if="wxPrefix === 1" :class="item">
            <Inner></Inner>
          </wx-camera>
          <camera v-else-if="wxPrefix === 2" :class="item">
            <Inner></Inner>
          </camera>
        </template>
        <template v-else-if="item === 'ad'">
          <wx-component v-if="!wxPrefix" :behavior="item" :class="item" unit-id="123" @error="onAdError"></wx-component>
          <wx-ad v-else-if="wxPrefix === 1" :class="item" unit-id="123" @error="onAdError"></wx-ad>
          <ad v-else-if="wxPrefix === 2" :class="item" unit-id="123" @error="onAdError"></ad>
        </template>
        <template v-else-if="item === 'official-account'">
          <wx-component v-if="!wxPrefix" :behavior="item" :class="item" @error="onOfficialAccountError"></wx-component>
          <wx-official-account v-else-if="wxPrefix === 1" :class="item" @error="onOfficialAccountError"></wx-official-account>
          <official-account v-else-if="wxPrefix === 2" :class="item" @error="onOfficialAccountError"></official-account>
        </template>
        <template v-else-if="item === 'web-view'">
          <wx-component v-if="!wxPrefix" :behavior="item" :class="item" src="https://www.qq.com/"></wx-component>
          <wx-web-view v-else-if="wxPrefix === 1" :class="item" src="https://www.qq.com/"></wx-web-view>
          <web-view v-else-if="wxPrefix === 2" :class="item" src="https://www.qq.com/"></web-view>
        </template>
        <template v-else-if="item === 'scroll-view'">
          <div>
            <wx-component v-if="!wxPrefix" :behavior="item" :class="item + '-y'" :scroll-into-view="'y1' + scrollView.yDest" :scroll-y="true" :scroll-with-animation="true" @scroll="onScrollViewScroll"><Inner2 type="y1"/></wx-component>
            <wx-scroll-view v-else-if="wxPrefix === 1" :class="item + '-y'" :scroll-into-view="'y2' + scrollView.yDest" :scroll-y="true" :scroll-with-animation="true" @scroll="onScrollViewScroll"><Inner2 type="y2"/></wx-scroll-view>
            <scroll-view v-else-if="wxPrefix === 2" :class="item + '-y'" :scroll-into-view="'y3' + scrollView.yDest" :scroll-y="true" :scroll-with-animation="true" @scroll="onScrollViewScroll"><Inner2 type="y3"/></scroll-view>
            <div class="scroll-view-btn" @click="onClickScrollViewYBtn">滚动到第三个滑块</div>
          </div>
          <div>
            <wx-component v-if="!wxPrefix" :behavior="item" :class="item + '-x'" :scroll-into-view="'x1' + scrollView.xDest" :scroll-x="true" :scroll-with-animation="true" @scroll="onScrollViewScroll"><Inner2 type="x1"/></wx-component>
            <wx-scroll-view v-else-if="wxPrefix === 1" :class="item + '-x'" :scroll-into-view="'x2' + scrollView.xDest" :scroll-x="true" :scroll-with-animation="true" @scroll="onScrollViewScroll"><Inner2 type="x2"/></wx-scroll-view>
            <scroll-view v-else-if="wxPrefix === 2" :class="item + '-x'" :scroll-into-view="'x3' + scrollView.xDest" :scroll-x="true" :scroll-with-animation="true" @scroll="onScrollViewScroll"><Inner2 type="x3"/></scroll-view>
            <div class="scroll-view-btn" @click="onClickScrollViewXBtn">滚动到第二个滑块</div>
          </div>
        </template>
        <!-- 不支持标签 -->
        <template v-else-if="item === 'xxxx'" >
          <wx-component v-if="!wxPrefix" :behavior="item"></wx-component>
          <wx-xxxx v-else-if="wxPrefix === 1"></wx-xxxx>
        </template>
        <iframe v-else-if="item === 'iframe'"></iframe>
      </div>
    </div>
  </div>
</template>

<script>
import Inner from './Inner.vue'
import Inner2 from './Inner2.vue'

export default {
  name: 'App',
  components: {
    Inner,
    Inner2,
  },
  data() {
    return {
      wxPrefix: 1, // 0 - wx-component 用法，1 - wx- 前缀用法，2 - 无前缀用法(需要配置 runtime.wxComponent 字段)
      list: [
        'normal',
        'img',
        'input',
        'textarea',
        'label',
        'video',
        'canvas',
        'view',
        'text',
        'button',
        'icon',
        'progress',
        'open-data',
        'navigator',
        'picker',
        'switch',
        'slider',
        'image',
        'map',
        'cover-view',
        'cover-image',
        'live-player',
        'live-pusher',
        'camera',
        'editor',
        'ad',
        'official-account',
        'scroll-view',
        // 'web-view',
        'xxxx',
        'iframe',
      ],
      icon: {
        size: [20, 30, 40, 50, 60, 70],
        color: ['red', 'orange', 'yellow', 'green', 'rgb(0,255,255)', 'blue', 'purple'],
        type: ['success', 'success_no_circle', 'info', 'warn', 'waiting', 'cancel', 'download', 'search', 'clear']
      },
      input: {
        inputText: '',
        inputNumber: '',
        inputRadio: 'radio2',
        inputCheckbox: true,
      },
      map: {
        markers: [{
          iconPath: 'https://i.loli.net/2019/07/27/5d3c141367f2784840.jpg',
          id: 0,
          latitude: 23.099994,
          longitude: 113.324520,
          width: 50,
          height: 50,
        }],
        polyline: [{
          points: [{
            longitude: 113.3245211,
            latitude: 23.10229,
          }, {
            longitude: 113.324520,
            latitude: 23.21229,
          }],
          color: '#FF0000DD',
          width: 2,
          dottedLine: true,
        }],
        controls: [{
          id: 1,
          iconPath: 'https://i.loli.net/2019/07/27/5d3c143497e6d38917.jpg',
          position: {
            left: 0,
            top: 300 - 50,
            width: 50,
            height: 50,
          },
          clickable: true,
        }],
      },
      scrollView: {
        yDest: '',
        xDest: '',
      },
    }
  },
  watch: {
    'input.inputText'(value) {
      console.log('input.inputText', value)
    },
    'input.inputNumber'(value) {
      console.log('input.inputNumber', value)
    },
    'input.inputRadio'(value) {
      console.log('input.inputRadio', value)
    },
    'input.inputCheckbox'(value) {
      console.log('input.inputCheckbox', value)
    },
  },
  created() {
    window.onDealWithNotSupportDom = dom => {
      dom.textContent = `标签 ${dom.tagName.toLowerCase()} 暂不支持`
    }
  },
  mounted() {
    const canvas = this.$refs.canvas[0]
    canvas.$$getContext().then(context => {
      context.setStrokeStyle("#00ff00")
      context.setLineWidth(5)
      context.rect(0, 0, 200, 200)
      context.stroke()
      context.setStrokeStyle("#ff0000")
      context.setLineWidth(2)
      context.moveTo(160, 100)
      context.arc(100, 100, 60, 0, 2 * Math.PI, true)
      context.moveTo(140, 100)
      context.arc(100, 100, 40, 0, Math.PI, false)
      context.moveTo(85, 80)
      context.arc(80, 80, 5, 0, 2 * Math.PI, true)
      context.moveTo(125, 80)
      context.arc(120, 80, 5, 0, 2 * Math.PI, true)
      context.stroke()
      context.draw()
    })

    canvas.$$getNodesRef().then(nodesRef => {
        nodesRef.boundingClientRect(res => {
          console.log('test $$getNodesRef', res)
        }).exec()
    })
  },
  methods: {
    onInput(evt) {
      console.log('onInput', evt.target.value, evt)
    },

    onTextareaInput(evt) {
      console.log('onTextareaInput', evt.target.value)
    },

    onImgLoad(evt) {
      console.log('onImgLoad')
    },

    onPickerChange(evt) {
      console.log('onPickerChange', evt.detail)
    },

    onMapMarkerTap(evt) {
      console.log('onMapMarkerTap', evt.detail)
    },

    onMapRegionChange(evt) {
      console.log('onMapRegionChange', evt.detail)
    },

    onMapControlTap(evt) {
      console.log('onMapControlTap', evt.detail)
    },

    onLivePlayerStateChange(evt) {
      console.log('onLivePlayerStateChange', evt.detail)
    },

    onLivePusherStateChange(evt) {
      console.log('onLivePusherStateChange', evt.detail)
    },

    onSwitchChange(evt) {
      console.log('onSwitchChange', evt.detail)
    },

    onLabelChange(evt) {
      console.log('onLabelChange', evt.detail)
    },

    onSliderChange(evt) {
      console.log('onSliderChange', evt.detail)
    },

    onEditorStatusChange(evt) {
      console.log('onEditorStatusChange', evt.detail)
    },

    onEditorReady(evt) {
      console.log('onEditorReady', evt.detail)
    },

    onAdError(evt) {
      console.log('onAdError', evt.detail)
    },

    onScrollViewScroll(evt) {
      console.log('onScrollViewScroll', evt.detail)
    },

    onClickScrollViewYBtn() {
      this.scrollView.yDest = 'block3'
    },

    onClickScrollViewXBtn() {
      this.scrollView.xDest = 'block2'
    },

    onOfficialAccountError(evt) {
      console.log('onOfficialAccountError', evt.detail)
    },
  }
}
</script>

<style>
.label {
  padding: 0 20px;
  height: 40px;
  line-height: 40px;
  background: rgba(7, 193, 96, 0.06);
}

.inline {
  display: inline;
}

.block {
  display: block;
}

.margin-left-10 {
  margin-left: 10px;
}

.comp {
  padding: 10px 20px;
}

.video {
  position: relative;
  width: 300px;
  height: 225px;
}

.map {
  width: 300px;
  height: 200px;
}

.live-player, .live-pusher, .camera {
  width: 300px;
  height: 225px;
}

.scroll-view-y {
  width: 100%;
  height: 125px;
}

.scroll-view-x {
  width: 300px;
  height: 125px;
}

.scroll-view-btn {
  margin: 10px 20px;
  text-align: center;
  background: #07c160;
  color: #fff;
  line-height: 30px;
  height: 30px;
  font-size: 16px;
  border-radius: 5px;
}
</style>

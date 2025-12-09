<template>
  <div style="width: 100%">
    <div>
      <iframe id="fream" src="/data/index.html" scrolling="auto" frameborder="0" style="width: 100%; height: 100vh;"></iframe>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  mounted() {
    // 等待iframe加载完成后操作DOM
    const iframe = document.getElementById('fream');
    iframe.onload = () => {
      this.fetchMeteorData(iframe.contentWindow.document);
      // 每5秒刷新一次数据
      setInterval(() => {
        this.fetchMeteorData(iframe.contentWindow.document);
      }, 5000);
    };
  },
  methods: {
    // 请求气象数据并更新iframe内的DOM
    fetchMeteorData(iframeDoc) {
      axios.get('http://localhost:5000/get_meteor_data')
        .then(res => {
          const data = res.data;
          // 逐个更新气象数据节点（匹配页面实际类名）
          iframeDoc.querySelector('.weather_text.text_one span:nth-child(1)').textContent = `温度：${data.temperature}℃`;
          iframeDoc.querySelector('.weather_text.text_one span:nth-child(2)').textContent = `湿度：${data.humidity}%`;
          iframeDoc.querySelector('.weather_text.text_one span:nth-child(3)').textContent = `风向：${data.windDirection}`;
          iframeDoc.querySelector('.weather_text.text_one span:nth-child(4)').textContent = `风速：${data.windSpeed}m/s`;
          iframeDoc.querySelector('.weather_text.text_two span:nth-child(1)').textContent = `降雨量：${data.rainfall}mm`;
          iframeDoc.querySelector('.weather_text.text_two span:nth-child(2)').textContent = `蒸发量：${data.evaporation}mm/h`;
          iframeDoc.querySelector('.weather_text.text_two span:nth-child(3)').textContent = `气压：${data.pressure}MPa`;
        })
        .catch(err => {
          console.error('获取气象数据失败：', err);
        });
    }
  }
};
</script>
<template>
  <h2>减脂计算器</h2>
  <p>
    <ul>
      <li><strong>理论指导：</strong>UP 主 <a target="_blank" href="https://space.bilibili.com/188669623">夹芯曲麒</a> 的视频 <a target="_blank" href="https://www.bilibili.com/video/BV1Sh411e7th">BV1Sh411e7th</a></li>
      <li>本计算器不会检查你输入的数据，请确保 5 项数据正确、完整</li>
      <li>计算在本地运行，数据保存在本地</li>
    </ul>
  </p>
  <hr>
  <form @submit.prevent="onSubmit">
    <select name="gender" id="gender" v-model.number="gender">
      <option value="" disabled>性别</option>
      <option value="1">男</option>
      <option value="2">女</option>
    </select>
    <input
      type="number"
      id="height"
      name="height"
      v-model.number="height"
      placeholder="身高 (cm)"
    />
    <input
      type="number"
      id="weight"
      name="weight"
      v-model.number="weight"
      placeholder="体重 (kg)"
    />
    <input
      type="number"
      id="age"
      name="age"
      v-model.number="age"
      placeholder="年龄"
    />
    <select name="activity" id="activity" v-model.number="activity">
      <option value="" disabled>每周运动强度</option>
      <option value="1">不运动</option>
      <option value="2">2-3次</option>
      <option value="3">4-6次</option>
    </select>
    <input type="submit" value="计算" />
  </form>
  <div id="result" v-if="showResult">
    <hr />
    <details open>
      <summary>基础信息</summary>
      <table>
        <tr>
          <th>基础代谢率 BMR</th>
          <td>{{ bmr }} 千卡</td>
        </tr>
        <tr>
          <th>平日每日理论摄入量</th>
          <td>{{ y }} 千卡</td>
        </tr>
        <tr>
          <th>减脂期每日理论摄入量</th>
          <td>{{ yDotSeven }} ± 200 千卡</td>
        </tr>
      </table>
    </details>
    <details open>
      <summary>营养分配</summary>
      <table>
        <tr>
          <th>蛋白质</th>
          <td>{{ protein }} 千卡</td>
          <td>{{ proteinGram }} 克</td>
        </tr>
        <tr>
          <th>脂肪</th>
          <td>{{ fatCarbon }} 千卡</td>
          <td>{{ fatGram }} 克</td>
        </tr>
        <tr>
          <th>碳水</th>
          <td>{{ fatCarbon }} 千卡</td>
          <td>{{ carbonGram }} 克</td>
        </tr>
      </table>
    </details>
    <details open>
      <summary>三餐分配</summary>
      <table>
        <thead>
          <th></th>
          <th>碳水</th>
          <th>脂肪</th>
          <th>蛋白质</th>
        </thead>
        <tr>
          <th>早餐</th>
          <td v-for="g in grams" :key="g">{{ Math.round(g * .2) }} 克</td>
        </tr>
        <tr>
          <th>午餐</th>
          <td v-for="g in grams" :key="g">{{ Math.round(g * .4) }} 克</td>
        </tr>
        <tr>
          <th>晚餐</th>
          <td v-for="g in grams" :key="g">{{ Math.round(g * .4) }} 克</td>
        </tr>
      </table>
    </details>
    <button @click="print">打印</button>
  </div>
  <hr>
  <p>Made with ❤️ by <a target="_blank" href="https://space.bilibili.com/24577998">高等小熊猫</a></p>
  <p><a href="https://webify.cloudbase.net/" target="_blank">CloudBase Webify</a> 提供网站托管服务</p>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      gender: "",
      height: null,
      weight: null,
      age: null,
      activity: "",
      showResult: false,
    };
  },
  mounted() {
    if (localStorage.gender) this.gender = localStorage.gender;
    if (localStorage.height) this.height = localStorage.height;
    if (localStorage.weight) this.weight = localStorage.weight;
    if (localStorage.age) this.age = localStorage.age;
    if (localStorage.activity) this.activity = localStorage.activity;
  },
  computed: {
    bmr() {
      const { weight: w, height: h, age, gender } = this;
      if (gender === 1) {
        return Math.round(67 + 13.73 * w + 5 * h - 6.9 * age);
      } else {
        return Math.round(661 + 9.6 * w + 1.72 * h - 4.7 * age);
      }
    },
    multiplier() {
      switch (this.activity) {
        case 1:
          return 1.1;
        case 2:
          return 1.3;
        case 3:
          return 1.5;
        default:
          return 1.1;
      }
    },
    y() {
      return Math.round(this.bmr * this.multiplier);
    },
    yDotSeven() {
      return Math.round(this.bmr * this.multiplier * 0.7);
    },
    protein() {
      return Math.round(this.yDotSeven * 0.4);
    },
    fatCarbon() {
      return Math.round(this.yDotSeven * 0.3);
    },
    proteinGram() {
      return Math.round(this.protein / 4);
    },
    fatGram() {
      return Math.round(this.fatCarbon / 9);
    },
    carbonGram() {
      return Math.round(this.fatCarbon / 4);
    },
    grams() {
      return [this.carbonGram, this.fatGram, this.proteinGram];
    },
  },
  methods: {
    onSubmit() {
      this.showResult = true;
      localStorage.gender = this.gender;
      localStorage.height = this.height;
      localStorage.weight = this.weight;
      localStorage.age = this.age;
      localStorage.activity = this.activity;
    },
    print() {
      const oldPage = document.body.innerHTML;
      const newPage = document.getElementById("result").innerHTML;
      document.body.innerHTML = newPage;
      window.print();
      window.location.reload();
      document.body.innerHTML = oldPage;
    },
  },
};
</script>

<template>
  <div class="json-processor">
    <h2 class="title">JSON小工具</h2>

    <div class="input-section">
      <label for="input-json">輸入JSON</label>
      <textarea id="input-json" v-model="inputJson" rows="10" placeholder="請貼上JSON内容"></textarea>
    </div>

    <div class="rules-section">
      <h3>修改規則：</h3>
      <div class="rules-list">
        <div v-for="(rule, index) in rules" :key="index" class="rule-item">
          <input v-model="rule.field" placeholder="要修改的字段" />
          <input v-model="rule.value" placeholder="新的值" />
          <button @click="removeRule(index)" class="btn btn-delete">刪除</button>
        </div>
      </div>
      <button @click="addRule" class="btn btn-add">添加規則</button>
    </div>

    <button @click="processJson" class="btn btn-process">處理JSON</button>

    <div v-if="outputJson" class="output-section">
      <div class="output-header">
        <h3>處理结果：</h3>
        <button @click="copyToClipboard" :class="['btn btn-copy', { 'btn-success': copySuccess }]">
          {{ copySuccess ? '已複製' : '複製' }}
        </button>
      </div>
      <textarea v-model="outputJson" readonly rows="10"></textarea>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue';

const inputJson = ref('');
const outputJson = ref('');
const rules = reactive([{ field: '', value: '' }]);
const copySuccess = ref(false);

const addRule = () => {
  rules.push({ field: '', value: '' });
};

const removeRule = (index) => {
  rules.splice(index, 1);
};

const processJson = () => {
  try {
    let data = JSON.parse(inputJson.value);

    // Apply all rules
    data.Data.forEach((item) => {
      rules.forEach((rule) => {
        if (rule.field && rule.value) {
          const targetItem = item.Item.find((subItem) => subItem.ItemColumn === rule.field);
          if (targetItem) {
            targetItem.ItemValue = rule.value;
          }
        }
      });
    });

    outputJson.value = JSON.stringify(data, null, 2);
    copyToClipboard();
  } catch (error) {
    outputJson.value = '处理JSON时出错：' + error.message;
  }
};

const copyToClipboard = () => {
  const textArea = document.createElement('textarea');
  textArea.value = outputJson.value;
  document.body.appendChild(textArea);
  textArea.select();
  try {
    document.execCommand('copy');
    copySuccess.value = true;
    setTimeout(() => {
      copySuccess.value = false;
    }, 2000);
  } catch (err) {
    console.error('Unable to copy to clipboard', err);
  }
  document.body.removeChild(textArea);
};
</script>

<style scoped>
.json-processor {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.title {
  font-size: 24px;
  font-weight: bold;
  color: #333;
  margin-bottom: 20px;
}

.input-section,
.rules-section,
.output-section {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #555;
}

textarea,
input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

textarea {
  resize: vertical;
}

.rules-list {
  margin-bottom: 10px;
}

.rule-item {
  display: flex;
  margin-bottom: 10px;
}

.rule-item input {
  flex: 1;
  margin-right: 10px;
}

.btn {
  padding: 10px 15px;

  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.btn-delete {
  background-color: #ff4d4d;
  color: white;
}

.btn-delete:hover {
  background-color: #ff3333;
}

.btn-add {
  background-color: #4caf50;
  color: white;
}

.btn-add:hover {
  background-color: #45a049;
}

.btn-process {
  background-color: #008cba;
  color: white;
  width: 100%;
}

.btn-process:hover {
  background-color: #007aa3;
}

.output-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.btn-copy {
  background-color: #f0f0f0;
  color: #333;
  margin: 10px 0;
}

.btn-copy:hover {
  background-color: #e0e0e0;
}

.btn-success {
  background-color: #4caf50;
  color: white;
}

h3 {
  font-size: 18px;
  color: #333;
  margin-bottom: 10px;
}
</style>

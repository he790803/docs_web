<template>
  <div>
    <textarea v-model="inputJson" rows="10" cols="50" placeholder="貼上JSON内容"></textarea>

    <div>
      <h3>修改規則：</h3>
      <div v-for="(rule, index) in rules" :key="index" class="rule-item">
        <input v-model="rule.field" placeholder="要修改的字段" />
        <input v-model="rule.value" placeholder="新的值" />
        <button @click="removeRule(index)">删除</button>
      </div>
      <button @click="addRule">添加規則</button>
    </div>

    <button @click="processJson">處理JSON</button>

    <div v-if="outputJson">
      <h3>處理结果：</h3>
      <pre>{{ outputJson }}</pre>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const inputJson = ref('');
    const outputJson = ref('');
    const rules = ref([{ field: '', value: '' }]);

    const addRule = () => {
      rules.value.push({ field: '', value: '' });
    };

    const removeRule = (index) => {
      rules.value.splice(index, 1);
    };

    const processJson = () => {
      try {
        let data = JSON.parse(inputJson.value);

        // 应用所有规则
        data.Data.forEach((item) => {
          rules.value.forEach((rule) => {
            if (rule.field && rule.value) {
              const targetItem = item.Item.find((subItem) => subItem.ItemColumn === rule.field);
              if (targetItem) {
                targetItem.ItemValue = rule.value;
              }
            }
          });
        });

        outputJson.value = JSON.stringify(data, null, 2);
      } catch (error) {
        outputJson.value = '處理錯誤：' + error.message;
      }
    };

    return {
      inputJson,
      outputJson,
      rules,
      addRule,
      removeRule,
      processJson,
    };
  },
};
</script>

<style scoped>
.rule-item {
  margin-bottom: 10px;
}
</style>

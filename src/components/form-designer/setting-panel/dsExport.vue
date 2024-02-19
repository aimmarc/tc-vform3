<template>
    <el-dialog title="导出数据源" v-model="visible" width="800px" :close-on-click-modal="false">
        <el-tabs type="border-card">
            <el-tab-pane label="选择导出数据源">User</el-tab-pane>
            <el-tab-pane label="导出结果预览">Config</el-tab-pane>
        </el-tabs>
        <template #footer>
            <el-button @click="close">关闭</el-button>
            <el-button type="primary">复制JSON</el-button>
        </template>
    </el-dialog>
</template>

<script setup>
import { ref } from 'vue';

const visible = ref(false);

function open() {
    // dsJson.value = '';
    visible.value = true;
}

function close() {
    visible.value = false;
}

defineExpose({
    open,
    close,
});
</script>

<template>
    <el-dialog v-model="visible" title="导入数据源" width="800px" :close-on-click-modal="false">
        <el-alert
            type="info"
            :closable="false"
            title="导入的数据源格式须符合规范，以保证顺利导入。"
        ></el-alert>
        <CodeEditor v-model="dsJson" :mode="'json'" :readonly="false" ref="codeEditor"></CodeEditor>
        <el-switch
            v-model="importMode"
            active-value="clear"
            inactive-value="add"
            active-text="导入后清空原有数据"
            inactive-text="追加到已有数据源之后"
        />
        <template #footer>
            <el-button @click="close">取消</el-button>
            <el-button type="primary" @click="handleImport">导入</el-button>
        </template>
    </el-dialog>
</template>

<script setup>
import { ref } from 'vue';
import CodeEditor from '@/components/code-editor/index';
import { deepClone } from '@/utils/util';

const emits = defineEmits(['import']);
const visible = ref(false);
const importMode = ref('clear');
const dsJson = ref('');

function open() {
    dsJson.value = '';
    visible.value = true;
}

function close() {
    visible.value = false;
}

function handleImport() {
    emits('import', deepClone(dsJson.value), importMode.value);
}

defineExpose({
    open,
    close,
});
</script>

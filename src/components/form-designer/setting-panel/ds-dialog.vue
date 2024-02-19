<template>
    <el-dialog
        v-model="visible"
        @close="close"
        width="800px"
        title="数据源设置"
        :close-on-click-modal="false"
    >
        <el-form
            ref="dsForm"
            :model="formData"
            :rules="rules"
            label-width="150px"
            label-position="left"
        >
            <el-form-item label="唯一名称" prop="uniqueName">
                <el-input v-model="formData.uniqueName" placeholder="请输入唯一名称" />
            </el-form-item>
            <el-form-item label="请求地址" prop="requestURL">
                <el-input v-model="formData.requestURL" placeholder="请输入请求地址" />
            </el-form-item>
            <el-form-item label="描述信息" prop="description">
                <el-input
                    v-model="formData.description"
                    type="textarea"
                    placeholder="请输入描述信息"
                />
            </el-form-item>
            <el-form-item label="请求方法" prop="requestMethod">
                <el-radio-group v-model="formData.requestMethod">
                    <el-radio-button v-for="item in methodLabels" :label="item" :key="item" />
                </el-radio-group>
            </el-form-item>
            <el-form-item label="请求头（headers）">
                <keyValEditorList add-btn-name="新增请求头" :key-val-list="formData.headers" />
            </el-form-item>
            <el-form-item label="参数（params）">
                <keyValEditorList add-btn-name="新增参数" :key-val-list="formData.params" />
            </el-form-item>
            <el-form-item label="发送数据（data）">
                <keyValEditorList add-btn-name="新增发送数据" :key-val-list="formData.data" />
            </el-form-item>
        </el-form>
        <el-tabs type="border-card">
            <el-tab-pane label="1. 请求配置">
                <el-alert
                    type="info"
                    :closable="false"
                    title="(config, isSandbox, DSV, VFR) => {"
                ></el-alert>
                <CodeEditor
                    :mode="'javascript'"
                    :readonly="false"
                    v-model="formData.configHandlerCode"
                    ref="configHandlerCodeEditor"
                ></CodeEditor>
                <el-alert type="info" :closable="false" title="}"></el-alert>
            </el-tab-pane>
            <el-tab-pane label="2. 数据处理">
                <el-alert
                    type="info"
                    :closable="false"
                    title="(result, isSandbox, DSV, VFR) => {"
                ></el-alert>
                <CodeEditor
                    :mode="'javascript'"
                    :readonly="false"
                    v-model="formData.dataHandlerCode"
                    ref="dataHandlerCodeEditor"
                ></CodeEditor>
                <el-alert type="info" :closable="false" title="}"></el-alert>
            </el-tab-pane>
            <el-tab-pane label="3. 错误处理">
                <el-alert
                    type="info"
                    :closable="false"
                    title="(error, isSandbox, DSV, $message, VFR) => {"
                ></el-alert>
                <CodeEditor
                    :mode="'javascript'"
                    :readonly="false"
                    v-model="formData.errorHandlerCode"
                    ref="errorHandlerCodeEditor"
                ></CodeEditor>
                <el-alert type="info" :closable="false" title="}"></el-alert>
            </el-tab-pane>
        </el-tabs>
        <template #footer>
            <!-- <el-button>测试数据源</el-button> -->
            <el-button @click="close">取消</el-button>
            <el-button type="primary" @click="handleSave">保存</el-button>
        </template>
    </el-dialog>
</template>

<script setup>
import { getDefaultDs } from '@/utils/util';
import { ref } from 'vue';
import keyValEditorList from './keyValEditorList.vue';
import CodeEditor from '@/components/code-editor/index';
import { ElMessage } from 'element-plus';
import { v4 as uuid } from 'uuid';

const emits = defineEmits(['submit']);
const rules = {
    uniqueName: [{ required: true, message: '请输入唯一名称', trigger: 'blur' }],
    requestURL: [{ required: true, message: '请输入请求地址', trigger: 'blur' }],
    requestMethod: [{ required: true, message: '请选择请求方法', trigger: 'blur' }],
};
const methodLabels = ['get', 'post', 'put', 'delete'];
const formData = ref(getDefaultDs());
const visible = ref(false);
const dsForm = ref();

const open = (detail) => {
    if (detail) {
        for (const key in formData.value) {
            formData.value[key] = detail[key];
        }
    } else {
        formData.value = getDefaultDs();
        formData.value.dataSourceId = `ds_${uuid()}`;
    }
    visible.value = true;
};
const close = () => {
    dsForm.value.clearValidate();
    visible.value = false;
};

function handleSave() {
    dsForm.value.validate((valid) => {
        if (valid) {
            emits('submit', JSON.parse(JSON.stringify(formData.value)));
        } else {
            ElMessage.warning('请完善表单！');
        }
    });
}

defineExpose({
    open,
    close,
});
</script>

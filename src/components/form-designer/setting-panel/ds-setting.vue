<template>
    <div class="ds-setting">
        <ul class="ds-list">
            <li
                v-for="(item, index) in dsList"
                :key="item.dataSourceId"
                class="mg-b_8 fs-sm flex flex-cross-center"
            >
                <el-icon class="mg-r_8">
                    <Platform />
                </el-icon>
                <span class="flex_1">{{ item.uniqueName }}</span>
                <el-button
                    icon="el-icon-edit"
                    circle
                    size="small"
                    @click="handleEditDs(index)"
                ></el-button>
                <el-button
                    icon="el-icon-delete"
                    circle
                    size="small"
                    @click="handleRemoveDs(index)"
                    type="danger"
                ></el-button>
            </li>
        </ul>
        <div class="ta-c">
            <el-button icon="el-icon-plus" text bg @click="handleAdd">新增数据源</el-button>
            <el-button
                :icon="Download"
                text
                bg
                title="导入数据源"
                @click="dsImport.open()"
            ></el-button>
            <el-button :icon="Upload" text bg title="导出数据源"></el-button>
        </div>
        <DsDialog ref="dsDialog" @submit="handleSubmit" />
        <DsImport ref="dsImport" @import="handleImport" />
    </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue';
import DsDialog from './ds-dialog.vue';
import { Platform, Download, Upload } from '@element-plus/icons-vue';
import { ElMessage, ElMessageBox } from 'element-plus';
import DsImport from './dsImport.vue';
import { v4 as uuid } from 'uuid';

const props = defineProps({
    designer: Object,
});
const dataSources = props.designer.formConfig.dataSources;
const dsDialog = ref();
const dsImport = ref();
const dsList = computed(() => {
    return dataSources || [];
});
let editingDsIndex = undefined;

const handleAdd = () => {
    editingDsIndex = undefined;
    dsDialog.value.open();
};

function handleSubmit(formData) {
    const dsNames = dataSources.map((item) => item.uniqueName);
    const isInclude = dsNames.includes(formData.uniqueName);
    if (editingDsIndex !== undefined) {
        if (isInclude && dsNames.indexOf(formData.uniqueName) !== editingDsIndex) {
            ElMessage.error('数据源名称已存在！');
            return;
        }
        dataSources.splice(editingDsIndex, 1, formData);
    } else {
        if (isInclude) {
            ElMessage.error('数据源名称已存在！');
            return;
        }
        dataSources.push(formData);
    }
    dsDialog.value.close();
}

function handleEditDs(index) {
    editingDsIndex = index;
    dsDialog.value.open(dataSources[index]);
}

function handleRemoveDs(index) {
    ElMessageBox.confirm('确认删除该数据源？', '提示', {
        cancelButtonText: '取消',
        confirmButtonText: '确认',
    }).then(() => {
        dataSources.splice(index, 1);
    });
}

function resolveImportDsItem(dsItem) {
    const dsNames = dataSources.map((item) => item.uniqueName);
    if (!dsNames.includes(dsItem.uniqueName)) {
        dsItem.dataSourceId = `ds_${uuid()}`;
        dataSources.push(dsItem);
    }
}

function handleImport(ds, type) {
    try {
        const dsObj = JSON.parse(ds);
        if (type === 'clear') dataSources.splice(0, dataSources.length);
        if (Array.isArray(dsObj)) {
            dsObj.forEach((el) => {
                resolveImportDsItem(el);
            });
        } else if (typeof dsObj === Object && !!dsObj) {
            resolveImportDsItem(dsObj);
        }
    } catch (err) {
        console.log('导入数据源失败:', err);
    }
    dsImport.value.close();
}
</script>

<style lang="scss" scoped>
.ds-setting {
    .ds-list {
        li {
            height: 32px;
            border-radius: 3px;
            border: 1px solid #eee;
            background-color: #f5f5f5;
            padding: 0 8px;
        }
    }
}
</style>

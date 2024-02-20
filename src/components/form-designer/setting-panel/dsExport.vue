<template>
    <el-dialog title="导出数据源" v-model="visible" width="1200px" :close-on-click-modal="false">
        <div class="ds-export-dialog-inner">
            <div class="flex">
                <el-card class="w_400" header="选择导出数据源">
                    <ul class="ds-list">
                        <li
                            v-for="(item, index) in dsList"
                            :key="item.dataSourceId"
                            class="mg-b_8 fs-sm flex flex-cross-center"
                        >
                            <el-icon class="mg-r_8">
                                <Platform class="black_4" />
                            </el-icon>
                            <span class="mg-r_10 black_4">{{ item.uniqueName }}</span>
                            <el-tooltip
                                class="box-item"
                                effect="dark"
                                :content="item.requestURL"
                                placement="top-start"
                            >
                                <span class="info flex_1 ellipsis">{{ item.requestURL }}</span>
                            </el-tooltip>
                            <el-checkbox v-model="checkedList[index].checked" />
                        </li>
                    </ul>
                </el-card>
                <div class="flex_1 mg-l_10">
                    <el-card header="导出结果预览">
                        <div class="bd_1">
                            <CodeEditor mode="json" readonly ref="codeEditor" />
                        </div>
                    </el-card>
                </div>
            </div>
        </div>
        <template #footer>
            <el-button @click="close">关闭</el-button>
            <el-button @click="handleDownloadJson">保存文件</el-button>
            <el-button type="primary" @click="handleCopyJson">复制JSON</el-button>
        </template>
    </el-dialog>
</template>

<script setup>
import { ref, watch } from 'vue';
import CodeEditor from '@/components/code-editor/index';
import { Platform } from '@element-plus/icons-vue';
import { copyToClipboard, deepClone, saveAsFile } from '@/utils/util';
import loadBeautifier from '@/utils/beautifierLoader';
import { ElMessage } from 'element-plus';

const props = defineProps({
    dsList: Array,
});
const visible = ref(false);
const checkedList = ref([]);
const codeEditor = ref();
let beautifierObj;

loadBeautifier((beautifier) => {
    beautifierObj = beautifier;
});

function open() {
    checkedList.value = props.dsList.map((item) => ({ ...item, checked: false }));
    visible.value = true;
}

function close() {
    visible.value = false;
}

function exportToJson(dsList = []) {
    const copyDsList = deepClone(dsList);
    copyDsList.forEach((el) => {
        delete el.checked;
    });
    codeEditor.value?.setValue?.(beautifierObj.js(JSON.stringify(copyDsList), { indent_size: 2 }));
}

function handleCopyJson(e) {
    copyToClipboard(codeEditor.value?.getValue(), e, ElMessage, '复制成功', '复制失败');
}

function handleDownloadJson() {
    saveAsFile(codeEditor.value?.getValue(), 'ds666.json');
}

watch(
    checkedList,
    (newVal) => {
        const needExportDsList = newVal.filter((x) => x.checked);
        exportToJson(needExportDsList);
    },
    {
        deep: true,
    },
);

defineExpose({
    open,
    close,
});
</script>

<style lang="scss">
.ds-export-dialog-inner {
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

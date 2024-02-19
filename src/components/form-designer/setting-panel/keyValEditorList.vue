<template>
    <div class="key-val-editor-list">
        <ul>
            <li v-for="(item, index) in keyValList">
                <keyValEditorItem :item="item" />
                <el-button icon="el-icon-delete" circle @click="handleRemove(index)"></el-button>
            </li>
        </ul>
        <el-button type="text" icon="el-icon-plus" @click="handleAdd">{{ addBtnName }}</el-button>
    </div>
</template>

<script setup>
import keyValEditorItem from './keyValEditorItem.vue';

const props = defineProps({
    keyValList: Array,
    addBtnName: {
        type: String,
        default: '添加',
    },
});

function handleAdd() {
    props.keyValList.push({
        name: '',
        type: 'String',
        value: '',
    });
}

function handleRemove(index) {
    props.keyValList.splice(index, 1);
}
</script>

<style lang="scss" scoped>
.key-val-editor-list {
    ul li {
        display: flex;
        margin-bottom: 14px;
    }
}
</style>

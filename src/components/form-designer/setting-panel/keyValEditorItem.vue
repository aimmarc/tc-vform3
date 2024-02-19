<template>
    <div class="key-val-editor-item">
        <el-form-item
            :rules="[{ trigger: 'blur', validator: () => requiredValidator(item.name, '名称') }]"
            prop="name"
        >
            <el-input placeholder="名称" v-model.trim="item.name" style="width: auto" />
        </el-form-item>
        <el-form-item>
            <el-select v-model="item.type" style="width: 120px" placeholder="请选择">
                <el-option
                    v-for="item in options"
                    :label="item.label"
                    :value="item.value"
                    :key="item.value"
                />
            </el-select>
        </el-form-item>
        <el-form-item
            :rules="[{ trigger: 'blur', validator: () => requiredValidator(item.value, '值') }]"
            prop="value"
        >
            <el-input placeholder="值" v-model.trim="item.value" style="width: auto" />
        </el-form-item>
    </div>
</template>

<script setup>
const props = defineProps({
    item: {
        type: Object,
        default: {
            name: '',
            type: '',
            value: '',
        },
    },
});

const options = [
    {
        label: '字符串类型',
        value: 'String',
    },
    {
        label: '数值类型',
        value: 'Number',
    },
    {
        label: '布尔类型',
        value: 'Boolean',
    },
    {
        label: '变量或表达式',
        value: 'Variable',
    },
];

function requiredValidator(value, field = '') {
    const errors = [];
    if (!value) {
        errors.push(new Error(`请输入${field}`));
    }
    return errors;
}
</script>

<style lang="scss" scoped>
.key-val-editor-item {
    display: flex;
    * {
        margin-right: 5px;
    }
}
</style>

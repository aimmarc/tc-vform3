<template>
    <el-form-item :label="i18nt('designer.setting.dsEnabled')">
        <el-switch v-model="optionModel.dsEnabled" />
    </el-form-item>
    <el-form-item :label="i18nt('designer.setting.dsName')" v-if="optionModel.dsEnabled">
        <el-select v-model="optionModel.dsName">
            <el-option
                v-for="item in dataSources"
                :key="item.dataSourceId"
                :label="item.uniqueName"
                :value="item.uniqueName"
            />
        </el-select>
    </el-form-item>
    <el-form-item label-width="0" v-else>
        <el-divider class="custom-divider-margin-top">
            {{ i18nt('designer.setting.optionsSetting') }}
        </el-divider>
        <option-items-setting
            :designer="designer"
            :selected-widget="selectedWidget"
        ></option-items-setting>
    </el-form-item>
</template>

<script>
import i18n from '@/utils/i18n';
import OptionItemsSetting from '@/components/form-designer/setting-panel/option-items-setting';

export default {
    name: 'optionItems-editor',
    mixins: [i18n],
    props: {
        designer: Object,
        selectedWidget: Object,
        optionModel: Object,
    },
    components: {
        OptionItemsSetting,
    },
    computed: {
        dataSources() {
            return this.designer.formConfig.dataSources || [];
        },
    },
};
</script>

<style scoped></style>

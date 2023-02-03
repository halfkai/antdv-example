
<script setup lang="ts">
import {
  Form,
  FormItem,
  Input,
  Select,
  SelectOption,
  Button,
  CheckboxGroup,
  Checkbox,
  RadioGroup,
  Radio,
} from 'ant-design-vue';
import { reactive, toRaw, ref, computed, watch } from 'vue';

const useForm = Form.useForm;
const modeRef = ref<'edit' | 'add'>()
const initModel = () => reactive({
  name: '',
  region: undefined,
  type: [],
})
const editModel = {
  name: 'editing',
  region: 'shanghai',
  type: ['1', '2']
}
const modelRef = computed(() => (modeRef.value === 'edit' ? editModel : initModel()))
const { resetFields, validate, validateInfos } = useForm(modelRef, reactive({
  name: [
    {
      required: true,
      message: 'Please input name',
    },
  ],
  region: [
    {
      required: true,
      message: 'Please select region',
    },
  ],
  type: [
    {
      required: true,
      message: 'Please select type',
      type: 'array',
    },
  ],
}), {
  onValidate: (...args) => console.log(...args),
});
watch(modeRef, (v) => {
  if (v === 'add') {
    resetFields()
  }
})
const onSubmit = () => {
  validate()
    .then(() => {
      console.log(toRaw(modelRef));
    })
    .catch(err => {
      console.log('error', err);
    });
};
const labelCol = { span: 4 }
const wrapperCol = { span: 14 }
</script>

<template>
  <RadioGroup v-model:value="modeRef">
    <Radio value="add">Add</Radio>
    <Radio value="edit">Edit</Radio>
  </RadioGroup>
  <Form style="margin-top: 1em" :label-col="labelCol" :wrapper-col="wrapperCol">
    <FormItem label="Activity name" v-bind="validateInfos.name">
      <Input v-model:value="modelRef.name" />
    </FormItem>
    <FormItem label="Activity zone" v-bind="validateInfos.region">
      <Select v-model:value="modelRef.region" placeholder="please select your zone">
        <SelectOption value="shanghai">Zone one</SelectOption>
        <SelectOption value="beijing">Zone two</SelectOption>
      </Select>
    </FormItem>
    <FormItem label="Activity type" v-bind="validateInfos.type">
      <CheckboxGroup v-model:value="modelRef.type">
        <Checkbox value="1" name="type">Online</Checkbox>
        <Checkbox value="2" name="type">Promotion</Checkbox>
        <Checkbox value="3" name="type">Offline</Checkbox>
      </CheckboxGroup>
    </FormItem>
    <FormItem :wrapper-col="{ span: 14, offset: 4 }">
      <Button type="primary" @click.prevent="onSubmit">Create</Button>
      <Button style="margin-left: 10px" @click="resetFields">Reset</Button>
    </FormItem>
  </Form>
</template>


<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" />
        <q-input v-model="tempData.age" label="年齡" />
        <q-btn color="primary" class="q-mt-md" @click.prevent="addPerson"
          >新增</q-btn
        >
      </div>
      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
  <Teleport to="body">
    <Alert
      :isOpnAlert="isOpnAlert"
      :alertWidth="alertWidth"
      @sendCloseAlert="closeAlert"
      :editTempData="editTempData"
      @sendEdit="handleClickOption"
    />
  </Teleport>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref, onMounted } from 'vue';
interface blockDataType {
  name: string;
  age: number;
}
const blockData = ref<blockDataType[]>([
  {
    name: 'test',
    age: 25,
  },
]);
interface tableConfigType {
  label: string;
  name: string;
  field: string;
  align: string;
}
const tableConfig = ref<tableConfigType[]>([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);
interface tableButtonType {
  label: string;
  icon: string;
  status: string;
}

const tableButtons = ref<tableButtonType[]>([
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
  },
]);

const tempData = ref({
  name: '',
  age: '',
});

const addPerson = () => {
  let query = {
    name: tempData.value.name,
    age: tempData.value.age,
  };
  const api = 'http://localhost:3000/posts';
  axios
    .post(api, query)
    .then((res) => {
      console.log(res);
      getPersonLists();
    })
    .catch((error) => console.log(error));
};
const getPersonLists = () => {
  const api = 'http://localhost:3000/posts';
  axios
    .get(api)
    .then((res) => {
      blockData.value = res.data;
    })
    .catch((error) => console.log(error));
};
const isOpnAlert = ref<boolean>(false);
const alertWidth = ref<string>(500);
const closeAlert = () => {
  isOpnAlert.value = false;
};
const editTempData = ref({
  name: '',
  age: '',
});
const handleClickOption = (
  btn: { lable: string; icon: string; states: string },
  data: { id: string; name: string; age: string }
) => {
  console.log(btn, data);
  if (btn.icon === 'edit') {
    const api = `http://localhost:3000/posts/${data.id}`;
    let query = {
      name: editTempData.value.name,
      age: editTempData.value.age,
    };
    axios
      .put(api, query)
      .then((res) => {
        isOpnAlert.value = true;
        getPersonLists();
        console.log(res);
      })
      .catch((error) => console.log(error));
  }
  if (btn.icon === 'delete') {
    const api = `http://localhost:3000/posts/${data.id}`;
    axios
      .delete(api)
      .then((res) => {
        // blockData.value.splice(data.id, 1);
        console.log(res);
        getPersonLists();
      })
      .catch((error) => console.log(error));
  }
};
onMounted(() => {
  getPersonLists();
});
</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>

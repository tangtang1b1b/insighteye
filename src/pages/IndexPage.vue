<template>
  <div class="shure" v-if="check">
    <div class="shureWrap">
      <p class="warn">刪除</p>
      <p class="content">是否確定刪除{{ memNum }}筆資料？</p>
      <div class="btnWrap">
        <div class="cancel" @click="dlCancel">取消</div>
        <div class="access" @click="dlAccess(isSelected)">確定</div>
      </div>
    </div>
  </div>
  <div class="typeArea" v-if="add">
    <div class="typeWrap">
      <input v-model="name" type="text" placeholder="姓名">
      <input v-model="phone" type="tel" placeholder="手機號碼">
      <input v-model="email" type="email" placeholder="電子信箱">
      <div class="gender">
        <span>性別 : </span>
        <input v-model="gender" type="radio" name="gender" value="男">男
        <input v-model="gender" type="radio" name="gender" value="女">女
      </div>
      <span>生日 : </span><input v-model="birthday" type="date">
    </div>
    <div class="btnWrap">
      <div class="cancel" @click="addCancel">取消</div>
      <div class="access" @click="addAccess">確定</div>
    </div>
  </div>
  <q-page class="row justify-left q-pa-lg">
    <div class="block-item">
      <div class="row justify-between items-center">
        <div class="text-h6">員工基本資訊</div>
        <div>
          <q-btn class="q-mr-sm" @click="addButton" color="primary" label="新增" />
          <q-btn color="white" @click="dlButton(isSelected)" text-color="black" label="刪除" />
        </div>
      </div>
      <QTable @getSelected="getSelected" :isSelected="isSelected" :columns="state.columns" :rows="state.rows" />
    </div>
  </q-page>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';
import QTable from 'src/components/QTable.vue';

export default {
  name: 'IndexPage',

  components: {
    QTable
  },

  setup() {
    const name = ref('');
    const phone = ref('');
    const email = ref('');
    const gender = ref('');
    const birthday = ref('');
    const rowsData = ref({});

    const unWrite = ref(false);
    const check = ref(false);
    const add = ref(false);
    const confirmed = ref(false);
    const isEmpty = () => {
      name.value = '';
      phone.value = '';
      email.value = '';
      gender.value = '';
      birthday.value = '';
    }
    const setValue = () => {
      rowsData.value = {
        name: name.value.trim() === '' || name.value.length === 0? unWrite.value = !unWrite.value : name.value,
        cellphone: phone.value.trim() === '' || phone.value.length === 0? unWrite.value = !unWrite.value : phone.value,
        email: email.value.trim() === '' || email.value.length === 0? unWrite.value = !unWrite.value : email.value,
        gender: gender.value.trim() === '' || gender.value.length === 0? unWrite.value = !unWrite.value : gender.value,
        birthday: birthday.value.trim() === '' || birthday.value.length === 0? unWrite.value = !unWrite.value : birthday.value.split('-').join('/'),
      }
    }
    const dlAccess = (el) => {
      const res = state.rows.filter((item) => {
        return !el.includes(item)
      })
      check.value = false;
      state.rows = res;
      isSelected.value = [];
    }
    const dlCancel = () => {
      check.value = false;
    }
    const memNum = ref(0);
    const dlButton = (el) => {
      if (el.length === 0) return
      memNum.value = el.length;
      check.value = true;
    }

    const addButton = (el) => {
      add.value = true;
    }
    const addAccess = () => {
      setValue();
      if(unWrite.value){
        alert('有欄位忘記填囉')
        unWrite.value = !unWrite.value;
        return
      }
      state.rows.unshift(rowsData.value);
      isEmpty();
      add.value = false;
    }
    const addCancel = () => {
      isEmpty();
      add.value = false;
    }

    const isSelected = ref([]);
    const getSelected = (data) => {
      isSelected.value = data;
    }

    const state = reactive({
      columns: [
        {
          name: 'name',
          label: '姓名',
          align: 'left',
          field: 'name',
          sortable: true
        },
        {
          name: 'cellphone',
          label: '手機',
          align: 'left',
          field: 'cellphone',
          sortable: true
        },
        {
          name: 'email',
          label: '信箱',
          align: 'left',
          field: 'email',
          sortable: true
        },
        {
          name: 'gender',
          label: '性別',
          align: 'left',
          field: 'gender',
          sortable: true
        },
        {
          name: 'birthday',
          label: '生日',
          align: 'left',
          field: 'birthday',
          sortable: true
        },
      ],
      rows: [],
    });

    const convertDate = (day) => {
      const datLength = [4, 2, 2];
      return day.split(/-|T/).slice(0, 3).map((date, index) => {
        return date.padStart(datLength[index], '0');
      }).join('/');
    }

    const memberData = async () => {
      fetch('http://35.194.177.50:7777/members')
        .then((res) => res.json())
        .then((data) => {
          data.members.map((item) => {
            item.birthday = convertDate(item.birthday);
          })
          state.rows = data.members;
        })
    }
    onMounted(async () => {
      await memberData();
    })

    return {
      unWrite, setValue, isEmpty, rowsData, name, phone, email, gender, birthday, addButton, state, getSelected, isSelected, dlButton, memNum, check, confirmed, dlAccess, dlCancel, add, addAccess, addCancel
    }
  }
};
</script>
<style lang="scss" scoped>
.block-item {
  min-width: 400px;

}

.typeArea {
  position: fixed;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: transparent;
  z-index: 100;

  .btnWrap {
    display: flex;
    justify-content: space-evenly;

    .cancel {
      margin: 10px;
      padding: 5px 40px;
      border: solid 2px #bec4ca;
      border-radius: 5px;
      cursor: pointer;
      display: flex;
      align-items: center;
      box-shadow: 0px 0px 3px #00000550;
    }

    .access {
      margin: 10px;
      padding: 5px 40px;
      cursor: pointer;
      border-radius: 5px;
      color: #fff;
      background-color: #2c88d9;
      display: flex;
      align-items: center;
      box-shadow: 0px 0px 3px #00000550;
    }
  }

  .typeWrap {
    display: flex;
    flex-direction: column;
    background-color: #fff;
    padding: 40px 50px;
    border: solid 2px #c3cfd9;
    border-radius: 5px;
    box-shadow: 0px 0px 3px #00000550;

    input {
      margin-bottom: 20px;
    }

    span {
      font-weight: bold;
    }
  }
}

.shure {
  position: fixed;
  background-color: transparent;
  width: 100vw;
  height: 100vh;
  z-index: 100;
  display: flex;
  justify-content: center;
  align-items: center;

  .shureWrap {
    color: #293845;
    border: solid 1px #c3cfd9;
    background-color: #fff;
    padding: 30px 60px;

    .warn {
      text-align: center;
      font-size: 36px;
    }

    .content {
      font-size: 24px;
    }

    .btnWrap {
      display: flex;
      justify-content: space-evenly;

      // outline: solid 1px #000;
      .cancel {
        padding: 5px 40px;
        border: solid 2px #bec4ca;
        border-radius: 5px;
        cursor: pointer;
        display: flex;
        align-items: center;
      }

      .access {
        padding: 5px 40px;
        cursor: pointer;
        border-radius: 5px;
        color: #fff;
        background-color: #2c88d9;
        display: flex;
        align-items: center;
      }
    }
  }
}
</style>

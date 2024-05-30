<template>
  <v-container>
    <v-sax-table :headers="headers" :items="items">
      <template v-slot:item.WorkingDay="{ item }">
        <label>{{ item.WorkingDay }}</label>
      </template>
      <template v-slot:item.SaleAmount="{ item }">
        <div :style="{ backgroundColor: item === selectedRow ? 'yellow' : 'white' }">
          <v-text-field
            v-if="item.editingSaleAmount"
            v-model="item.SaleAmount"
            @focusout="finishEditing(item, 'SaleAmount')"
            @keyup.enter="moveFocus(item, 'SaleAmount')"
            hide-details
            solo
          ></v-text-field>
          <label v-else @click="editField(item, 'SaleAmount')">{{ item.SaleAmount }}</label>
        </div>
      </template>
      <template v-slot:item.CustomerCount="{ item }">
        <div :style="{ backgroundColor: item === selectedRow ? 'yellow' : 'white' }">
          <v-text-field
            v-if="item.editingCustomerCount"
            v-model="item.CustomerCount"
            @focusout="finishEditing(item, 'CustomerCount')"
            @keyup.enter="moveFocus(item, 'CustomerCount')"
            hide-details
            solo
          ></v-text-field>
          <label v-else @click="editField(item, 'CustomerCount')">{{ item.CustomerCount }}</label>
        </div>
      </template>
    </v-sax-table>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      headers: [
        { text: 'Working Day', value: 'WorkingDay' },
        { text: 'Sale Amount', value: 'SaleAmount' },
        { text: 'Customer Count', value: 'CustomerCount' }
      ],
      items: [
        {
          WorkingDay: 'Monday',
          SaleAmount: 100,
          CustomerCount: 10,
          editingSaleAmount: false,
          editingCustomerCount: false
        },
        {
          WorkingDay: 'Tuesday',
          SaleAmount: 150,
          CustomerCount: 15,
          editingSaleAmount: false,
          editingCustomerCount: false
        }
        // 更多数据
      ],
      selectedRow: null
    }
  },
  methods: {
    editField(item, field) {
      this.resetEditing()
      this.selectedRow = item
      item[`editing${field}`] = true
    },
    finishEditing(item, field) {
      item[`editing${field}`] = false
    },
    moveFocus(item, field) {
      if (field === 'SaleAmount') {
        item.editingSaleAmount = false
        item.editingCustomerCount = true
      } else if (field === 'CustomerCount') {
        item.editingCustomerCount = false
        const nextIndex = this.items.indexOf(item) + 1
        if (nextIndex < this.items.length) {
          this.selectedRow = this.items[nextIndex]
          this.items[nextIndex].editingSaleAmount = true
        } else {
          this.addItem()
        }
      }
    },
    addItem() {
      const newItem = {
        WorkingDay: '',
        SaleAmount: 0,
        CustomerCount: 0,
        editingSaleAmount: true,
        editingCustomerCount: false,
        NewLine: true
      }
      this.items.push(newItem)
      this.selectedRow = newItem
    },
    resetEditing() {
      this.items.forEach((item) => {
        item.editingSaleAmount = false
        item.editingCustomerCount = false
      })
    },
    deleteRow(event) {
      if (event && event.key === 'Delete') {
        if (this.selectedRow) {
          this.selectedRow.disabled = true; // 禁用选中行
          this.selectedRow = null; // 清空选中行
        }
      }
    },
    moveFocusNext(event) {
      if (this.selectedRowIndex !== null && this.selectedColIndex !== null) {
        event.preventDefault(); // 阻止默认的回车行为
        if (this.selectedColIndex === this.headers.length - 1) { // 如果是最后一列
          if (this.selectedRowIndex < this.items.length - 1) { // 如果不是最后一行
            this.selectedRowIndex++;
            this.selectedColIndex = 0; // 切换到下一行的第一列
          }
        } else { // 不是最后一列
          this.selectedColIndex++; // 切换到下一列
        }
        // 根据新的选中行和列进行处理，比如编辑字段或者设置焦点等
        his.handleSelectedCell();
      }
    },    
    handleClickNewRow() {
      // 将新行的背景色设为黄色，其他行恢复默认
      this.items.forEach((item, index) => {
        if (index === this.selectedRowIndex) {
          // 设置新行的背景色为黄色
          item.backgroundColor = 'yellow';
        } else {
          // 其他行恢复默认背景色
          item.backgroundColor = 'white';
        }
      });
    },
    handleSelectedCell() {
      // 根据选中行和列处理对应的操作，比如编辑字段或者设置焦点等
    }
  }
}
</script>

<style>
/* 这里可以添加额外的样式 */
</style>

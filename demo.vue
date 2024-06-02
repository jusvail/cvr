<template>
    <v-container>
      <v-data-table
        :headers="headers"
        :items="items"
        class="elevation-1"
      >
        <template v-slot:item="{ item, index }">
          <tr
            :class="{
              'highlight-row': selectedRow === index && !item.deleted,
              'deleted-row': item.deleted,
              'deleted-highlight-row': selectedRow === index && item.deleted
            }"
            @click="selectRow(index)"
          >
            <td>
              <v-text-field
                v-if="item.editing && currentField === 'WorkingDate' && currentItem === index"
                v-model="item.WorkingDate"
                @blur="finishEditing(item, 'WorkingDate')"
                @keyup.enter="moveFocus(item, 'WorkingDate')"
                ref="WorkingDate"
                hide-details
                solo
              ></v-text-field>
              <span v-else @click="editField(item, index, 'WorkingDate')">{{ item.WorkingDate }}</span>
            </td>
            <td>
              <v-text-field
                v-if="item.editing && currentField === 'LastSaleAmount' && currentItem === index"
                v-model="item.LastSaleAmount"
                @blur="finishEditing(item, 'LastSaleAmount')"
                @keyup.enter="moveFocus(item, 'LastSaleAmount')"
                ref="LastSaleAmount"
                hide-details
                solo
              ></v-text-field>
              <span v-else @click="editField(item, index, 'LastSaleAmount')">{{ item.LastSaleAmount }}</span>
            </td>
            <td>
              <v-text-field
                v-if="item.editing && currentField === 'LastSaleNumbers' && currentItem === index"
                v-model="item.LastSaleNumbers"
                @blur="finishEditing(item, 'LastSaleNumbers')"
                @keyup.enter="moveFocus(item, 'LastSaleNumbers')"
                ref="LastSaleNumbers"
                hide-details
                solo
              ></v-text-field>
              <span v-else @click="editField(item, index, 'LastSaleNumbers')">{{ item.LastSaleNumbers }}</span>
            </td>
            <td>
              <v-text-field
                v-if="item.editing && currentField === 'SaleAmount' && currentItem === index"
                v-model="item.SaleAmount"
                @blur="finishEditing(item, 'SaleAmount')"
                @keyup.enter="moveFocus(item, 'SaleAmount')"
                ref="SaleAmount"
                hide-details
                solo
              ></v-text-field>
              <span v-else @click="editField(item, index, 'SaleAmount')">{{ item.SaleAmount }}</span>
            </td>
            <td>
              <v-text-field
                v-if="item.editing && currentField === 'SaleNumbers' && currentItem === index"
                v-model="item.SaleNumbers"
                @blur="finishEditing(item, 'SaleNumbers')"
                @keyup.enter="moveFocus(item, 'SaleNumbers')"
                ref="SaleNumbers"
                hide-details
                solo
              ></v-text-field>
              <span v-else @click="editField(item, index, 'SaleNumbers')">{{ item.SaleNumbers }}</span>
            </td>
          </tr>
        </template>
      </v-data-table>
    </v-container>
  </template>
  
  <script>
  export default {
    props: {
      items: {
        type: Array,
        required: true
      }
    },
    data() {
      return {
        headers: [
          { text: 'Working Date', value: 'WorkingDate' },
          { text: 'Last Sale Amount', value: 'LastSaleAmount' },
          { text: 'Last Sale Numbers', value: 'LastSaleNumbers' },
          { text: 'Sale Amount', value: 'SaleAmount' },
          { text: 'Sale Numbers', value: 'SaleNumbers' }
        ],
        currentItem: null,
        currentField: '',
        selectedRow: null
      }
    },
    methods: {
      selectRow(index) {
        this.selectedRow = index;
      },
      editField(item, index, field) {
        if (item.deleted) return; // Prevent editing deleted rows
        this.resetEditing();
        this.currentItem = index;
        this.currentField = field;
        item.editing = true;
        this.$nextTick(() => {
          const input = this.$refs[field][index];
          if (input) {
            input.focus();
            input.select();
          }
        });
      },
      finishEditing(item, field) {
        item.editing = false;
      },
      moveFocus(item, field) {
        const fields = ['WorkingDate', 'LastSaleAmount', 'LastSaleNumbers', 'SaleAmount', 'SaleNumbers'];
        let currentIndex = fields.indexOf(field);
        if (currentIndex < fields.length - 1) {
          this.finishEditing(item, field);
          this.editField(item, this.currentItem, fields[currentIndex + 1]);
        } else {
          const currentIndexInItems = this.currentItem;
          if (currentIndexInItems < this.items.length - 1) {
            this.finishEditing(item, field);
            this.editField(this.items[currentIndexInItems + 1], currentIndexInItems + 1, fields[0]);
          } else {
            this.addNewRow();
          }
        }
      },
      addNewRow() {
        const newItem = {
          WorkingDate: '',
          LastSaleAmount: '',
          LastSaleNumbers: '',
          SaleAmount: '',
          SaleNumbers: '',
          editing: false,
          deleted: false
        };
        this.items.push(newItem);
        this.$nextTick(() => {
          const newIndex = this.items.length - 1;
          this.editField(newItem, newIndex, 'WorkingDate');
          this.selectRow(newIndex);  // Select the new row and highlight it
        });
      },
      resetEditing() {
        this.items.forEach(item => {
          item.editing = false;
        });
        this.currentItem = null;
        this.currentField = '';
      },
      handleKeyDown(event) {
        if (event.key === 'Delete' && this.selectedRow !== null) {
          this.items[this.selectedRow].deleted = true;
          this.selectedRow = null;
        }
      }
    },
    mounted() {
      window.addEventListener('keydown', this.handleKeyDown);
    },
    beforeDestroy() {
      window.removeEventListener('keydown', this.handleKeyDown);
    }
  }
  </script>
  
  <style scoped>
  .highlight-row {
    background-color: yellow;
  }
  .deleted-row {
    background-color: gray;
  }
  .deleted-highlight-row {
    background-color: lightgray;
  }
  </style>
  

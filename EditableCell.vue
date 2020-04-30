//在父组件中使用
// <el-table-column label="昵称" width="200">
//     <editable-cell
//     slot-scope="{row}"
//     :can-edit="editModeEnabled"
//     @inputComplate="inputComplate"
//     v-model="row.PetName"
//     >
//         <span slot="content">{{row.PetName}}</span>
//     </editable-cell>
// </el-table-column>

<template>
  <div @dblclick="onFielddblClick" class="edit-cell">
    <el-tooltip
      v-if="!editMode && !showInput"
      :placement="toolTipPlacement"
      :open-delay="toolTipDelay"
      :content="toolTipContent"
    >
      <div
        tabindex="0"
        class="cell-content"
        :class="{'edit-enabled-cell': canEdit}"
        @keyup.enter="onFielddblClick"
      >
        <slot name="content"></slot>
      </div>
    </el-tooltip>
    <component
      :is="editableComponent"
      v-if="editMode || showInput"
      ref="input"
      @focus="onFielddblClick"
      @keyup.enter.native="$event.target.blur"
      @blur="onInputExit"
      v-on="listeners"
      v-bind="$attrs"
      v-model="model"
    >
      <slot name="edit-component-slot"></slot>
    </component>
  </div>
</template>
<script>
export default {
  name: "editable-cell",
  inheritAttrs: false,
  props: {
    value: {
      type: String,
      default: ""
    },
    toolTipContent: {
      type: String,
      default: "双击编辑"
    },
    toolTipDelay: {
      type: Number,
      default: 500
    },
    toolTipPlacement: {
      type: String,
      default: "top-start"
    },
    showInput: {
      type: Boolean,
      default: false
    },
    editableComponent: {
      type: String,
      default: "el-input"
    },
    closeEvent: {
      type: String,
      default: "change"
    },
    canEdit: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      editMode: false,
      tempVal: ""
    };
  },
  computed: {
    model: {
      get() {
        return this.value;
      },
      set(val) {
        this.$emit("input", val);
        this.tempVal = val;
      }
    },
    listeners() {
      return {
        [this.closeEvent]: this.onInputExit,
        ...this.$listeners
      };
    }
  },
  methods: {
    onFielddblClick() {
      if (this.canEdit) {
        this.editMode = true;
        this.$nextTick(() => {
          let inputRef = this.$refs.input;
          if (inputRef && inputRef.focus) {
            inputRef.focus();
          }
        });
      }
    },
    onInputExit() {
      this.editMode = false;
      if(this.tempVal!="")
      this.$emit("inputComplate",this.tempVal);
    }
    // onInputChange(val) {
    //   this.$emit("input", val);
    // }
  }
};
</script>

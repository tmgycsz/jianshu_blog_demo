<template>
  <transition name="el-fade-in-linear">
    <div class="dialog_box" v-if="dialogVisible">
      <div class="dialog_mask"></div>
      <div
        class="loading_wrap"
        v-if="confirmLoading"
      ></div>
      <div
        :style="{ width: dialogWidth }"
        class="normal_dialog"
        v-drag
      >
        <div class="dialog_header">
          <div class="header_title fl">{{ title }}</div>
          <div class="header_button_box fr">
            <i
              class="el-icon-close"
              @click="closeDialog"
            />
          </div>
        </div>
        <div
          :style="{ height: bodyHeight }"
          class="dialog_body"
        >
          <!-- 弹窗中心区域 -->
          <slot/>
        </div>
        <div
          class="dialog_footer"
          :style="{ 'textAlign': footerAlign }"
        >
          <el-button
            plain
            class="edit-dialog-btn"
            size="small"
            type="info"
            @click="cancel"
          >{{ cancelText }}
          </el-button>
          <el-button
            v-if="dialogType === 'confirm'"
            class="edit-dialog-btn"
            size="small"
            type="primary"
            :loading="confirmLoading"
            @click="confirm"
          >{{ confirmText }}
          </el-button>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: "DragDialog",
  props: {
    dialogShow: {
      type: Boolean,
      default: false
    },
    dialogWidth: {
      type: String,
      default: "500px"
    },
    bodyHeight: {
      type: String,
      default: "300px"
    },
    title: {
      type: String,
      default: "提示"
    },
    dialogType: {
      type: String,
      default: "confirm"
    },
    footerAlign: {
      type: String,
      default: "center"
    },
    confirmText: {
      type: String,
      default: "确定"
    },
    confirmLoading: {
      type: Boolean,
      default: false
    },
    cancelText: {
      type: String,
      default: "取消"
    }
  },
  data() {
    return {
      // 弹窗的显示隐藏
      dialogVisible: false
    };
  },
  watch: {
    // 双向监听，监听父组件传入的改变和子组件自主触发的改变
    dialogShow: {
      handler() {
        this.dialogVisible = this.dialogShow;
      },
      immediate: true
    },
    dialogVisible: {
      handler(val) {
        this.$emit("update:showDialog", val);
      }
    }
  },
  created() {
  },
  methods: {
    // emit按钮的点击事件给父组件
    cancel() {
      this.$emit("cancel");
    },
    confirm() {
      this.$emit("confirm");
    },
    // 关闭弹窗
    closeDialog() {
      this.dialogVisible = false;
    }
  },
  directives: {
    drag: {
      inserted(el, binding, vnode) {
        vnode = vnode.elm;
        el.onmousedown = ((event) => {
          if (event.target.className !== "dialog_header") {
            return;
          }
          //获取鼠标在盒子中的位置
          let mouseX = event.clientX - vnode.offsetLeft;
          let mouseY = event.clientY - vnode.offsetTop;
          //绑定移动和停止函数
          document.onmousemove = ((event) => {
            let left, top;
            //获取新的鼠标位置对应下的盒子应该在的位置
            left = event.clientX - mouseX;
            top = event.clientY - mouseY;
            //获取div在页面中X轴的最小最大位置
            let minX = vnode.offsetWidth / 2;
            let maxX = window.innerWidth - (vnode.offsetWidth / 2);
            if (left <= minX) {
              left = minX;
            } else if (left >= maxX) {
              left = maxX;
            }
            //获取div在页面中Y轴的最大最小位置
            let minY = vnode.offsetHeight / 2;
            let maxY = window.innerHeight - (vnode.offsetHeight / 2);
            if (top <= minY) {
              top = minY;
            } else if (top >= maxY) {
              top = maxY;
            }
            //赋值移动
            vnode.style.left = left + "px";
            vnode.style.top = top + "px";
          });
          document.onmouseup = (() => {
            document.onmousemove = document.onmouseup = null;
          });
        });
        window.onresize = (() => {
          vnode.style.left = "50%";
          vnode.style.top = "50%";
        });
      }
    }
  }
};
</script>

<style
  type="text/less"
  lang="less"
>
.dialog_box {
  position: fixed;
  z-index: 99;
  left: 0;
  top: 0;
  bottom: 0;
  right: 0;

  .dialog_mask {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    background-color: #000;
    opacity: 0.5;
  }

  .loading_wrap {
    position: absolute;
    z-index: 999;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    background: transparent;
  }

  .normal_dialog {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: #FFFFFF;
    box-shadow: 0px 0px 0px 0px;
    border-radius: 10px;
    border: 1px solid #DDDDDD;
    z-index: 1000;
    box-sizing: border-box;

    .dialog_header {
      overflow: hidden;
      height: 50px;
      line-height: 55px;
      padding: 0 15px;
      font-size: 15px;
      color: #000000;
      border-bottom: 1px solid #EEEEEE;
      cursor: move;

      .header_button_box {
        cursor: pointer;

        i {
          display: inline-block;
          font-size: 18px;
          color: #666666;
          margin-left: 10px;
        }
      }
    }

    .dialog_body {
      color: #606266;
      font-size: 14px;
      overflow: auto;
    }

    .dialog_footer {
      width: 100%;
      border-top: 1px solid #EEEEEE;
      padding: 10px;
      box-sizing: border-box;

      .edit-dialog-btn {
        width: 70px;
      }
    }
  }
}
</style>
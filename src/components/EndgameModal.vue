<script setup>
import { defineProps, defineEmits} from "vue";

defineProps({
  isOpen: Boolean,
  result: String,
});

const emit = defineEmits(["modalClose"]);
</script>

<template>
  <div v-if="isOpen" class="modal-mask">
    <div class="modal-wrapper">
      <div class="modal-container" @click="emit('modalClose')">
        <div class="modal-header">
          <slot name="header"> result: {{ result }} </slot>
        </div>
        <div class="modal-body">
          <slot name="content"> Close to play again </slot>
        </div>
        <div class="modal-footer">
          <slot name="footer">
            <div>
              <button @click="emit('modalClose')">Close</button>
            </div>
          </slot>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}
.modal-container {
  width: 300px;
  margin: 150px auto;
  padding: 20px 30px;
  background-color: #fff;
  color: black;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
}

</style>
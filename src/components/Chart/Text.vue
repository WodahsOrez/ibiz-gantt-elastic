<!--
/**
 * @fileoverview Text component
 * @license MIT
 * @author Rafal Pospiech <neuronet.io@gmail.com>
 * @package GanttElastic
 */
-->
<template>
  <svg
    class="gantt-elastic__chart-row-text-wrapper"
    :style="{ ...root.style['chart-row-text-wrapper'] }"
    :x="(isNaN(task.x + task.width + root.state.options.chart.text.offset) ? 0 : (task.x + task.width + root.state.options.chart.text.offset))"
    :y="(isNaN(task.y - root.state.options.chart.grid.horizontal.gap) ? 0 : (task.y - root.state.options.chart.grid.horizontal.gap))"
    :width="(isNaN(getWidth) ? 0 : getWidth)"
    :height="(isNaN(getHeight) ? 0 : getHeight)"
  >
    <foreignObject x="0" y="0" width="100%" :height="getHeight">
      <div
        xmlns="http://www.w3.org/1999/xhtml"
        class="gantt-elastic__chart-row-text"
        :style="{ ...root.style['chart-row-text'] }"
      >
        <div
          class="gantt-elastic__chart-row-text-content gantt-elastic__chart-row-text-content--text"
          :style="{
            ...root.style['chart-row-text-content'],
            ...root.style['chart-row-text-content--text'],
            ...contentStyle,
            ...taskTextStyle
          }"
          v-if="!html"
        >
          <div>{{ label }}</div>
        </div>
        <div
          class="gantt-elastic__chart-row-text-content gantt-elastic__chart-row-text-content--html"
          :style="{
            ...root.style['chart-row-text-content'],
            ...root.style['chart-row-text-content--html'],
            ...contentStyle,
            ...taskTextStyle
          }"
          v-if="html"
          v-html="label"
        ></div>
      </div>
    </foreignObject>
  </svg>
</template>

<script>
export default {
  name: 'ChartText',
  inject: ['root'],
  props: ['task'],
  data() {
    return {};
  },
  computed: {
    /**
     * Get width
     *
     * @returns {number}
     */
    getWidth() {
      const textStyle = this.root.style['chart-row-text'];
      this.root.state.ctx.font = `${textStyle['font-weight']} ${textStyle['font-size']} ${textStyle['font-family']}`;
      const textWidth = this.root.state.ctx.measureText(this.task.label).width;
      return textWidth + this.root.state.options.chart.text.xPadding * 2;
    },

    /**
     * Get height
     *
     * @returns {number}
     */
    getHeight() {
      return this.task.height + this.root.state.options.chart.grid.horizontal.gap * 2;
    },

    /**
     * Get content style
     *
     * @returns {object}
     */
    contentStyle() {
      return { height: '100%', 'line-height': this.getHeight + 'px' };
    },

    /**
     * Should we render text as html?
     *
     * @returns {boolean}
     */
    html() {
      const cols = this.root.state.options.taskList.columns;
      var labelName = this.root.state.options.taskList.labelField;
      for (let i = 0, len = cols.length; i < len; i++) {
        const col = cols[i];
        if ((col.value === 'label' || col.value == labelName) && typeof col.html !== 'undefined' && col.html) {
          return true;
        }
      }
      return false;
    },
    taskTextStyle() {
      if (this.task.style) {
        return this.task.style['text']
      }
      return {}
    },
    label() {
      var labelName = this.root.state.options.taskList.labelField;
      var label = 'label';
      if (labelName) {
        label = labelName;
      }
      var columns = this.root.state.options.taskList.columns;
      var col = columns.find(col => Object.is(col.value, label));
      if (typeof col.render === 'function') {
        return col.render(this.task);
      }
      return this.task[label];
    }
  }
};
</script>

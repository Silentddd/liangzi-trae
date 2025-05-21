<template>
  <div class="app-container">
    <h1>量子计算模拟器</h1>
    <div class="quantum-state-container">
      <div class="bloch-sphere-container">
        <!-- 布洛赫球可视化将在这里实现 -->
        <canvas ref="blochCanvas" width="400" height="400"></canvas>
      </div>
      <div class="controls">
        <el-form label-position="top">
          <el-form-item label="量子位状态">
            <el-input-number v-model="alpha" :min="0" :max="1" :step="0.01" :precision="2" controls-position="right" @change="handleInputChange" />
            <el-input-number v-model="beta" :min="0" :max="1" :step="0.01" :precision="2" controls-position="right" @change="handleInputChange" />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="updateQuantumState">更新状态</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      alpha: 1,  // |0⟩状态的系数
      beta: 0    // |1⟩状态的系数
    }
  },
  mounted() {
    this.drawBlochSphere();
  },
  methods: {
    handleInputChange() {
      // 确保alpha和beta的值在0-1范围内
      this.alpha = Math.max(0, Math.min(1, this.alpha));
      this.beta = Math.max(0, Math.min(1, this.beta));
      
      // 确保alpha和beta的平方和不超过1
      const sum = this.alpha * this.alpha + this.beta * this.beta;
      if (sum > 1) {
        const factor = Math.sqrt(1 / sum);
        this.alpha *= factor;
        this.beta *= factor;
        this.$message.warning('输入值已自动调整以满足归一化条件');
      } else if (sum === 0) {
        this.alpha = 1;
        this.beta = 0;
        this.$message.warning('输入值不能全为零，已重置为默认状态');
      }
      this.drawBlochSphere();
    },
    drawBlochSphere() {
      const canvas = this.$refs.blochCanvas;
      const ctx = canvas.getContext('2d');
      
      // 清空画布
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      
      // 绘制布洛赫球
      ctx.beginPath();
      ctx.arc(200, 200, 150, 0, 2 * Math.PI);
      ctx.strokeStyle = '#409EFF';
      ctx.stroke();
      
      // 绘制坐标轴
      this.drawAxis(ctx);
      
      // 绘制量子态向量
      this.drawQuantumState(ctx);
    },
    drawAxis(ctx) {
      // 绘制X,Y,Z坐标轴
      ctx.beginPath();
      ctx.moveTo(50, 200);
      ctx.lineTo(350, 200);
      ctx.moveTo(200, 50);
      ctx.lineTo(200, 350);
      ctx.strokeStyle = '#909399';
      ctx.stroke();
      
      // 添加标签
      ctx.font = '16px Arial';
      ctx.fillStyle = '#909399';
      ctx.fillText('X', 360, 200);
      ctx.fillText('Y', 200, 40);
    },
    drawQuantumState(ctx) {
      // 根据alpha和beta计算量子态在布洛赫球上的位置
      // 实现量子态到布洛赫球坐标的转换
      const theta = 2 * Math.acos(this.alpha);
      const phi = Math.atan2(this.beta.imaginary || 0, this.beta.real || this.beta);
      
      const x = Math.sin(theta) * Math.cos(phi);
      const y = Math.sin(theta) * Math.sin(phi);
      const z = Math.cos(theta);
      
      // 绘制量子态点
      ctx.beginPath();
      ctx.arc(200 + x * 150, 200 - y * 150, 5, 0, 2 * Math.PI);
      ctx.fillStyle = '#F56C6C';
      ctx.fill();
    },
    updateQuantumState() {
      // 归一化量子态
      const norm = Math.sqrt(this.alpha * this.alpha + this.beta * this.beta);
      this.alpha /= norm;
      this.beta /= norm;
      
      // 重新绘制布洛赫球
      this.drawBlochSphere();
      
      // 显示更新成功提示
      this.$message.success('量子态已成功更新');
    }
  }
}
</script>

<style>
.app-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}
.quantum-state-container {
  display: flex;
  gap: 20px;
}
.bloch-sphere-container {
  flex: 1;
}
.controls {
  flex: 1;
}
</style>
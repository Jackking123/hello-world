# 图像信噪比（SNR）定义与计算

## 1. 基本定义与物理含义

**SNR（Signal-to-Noise Ratio，信噪比）** 是衡量信号质量的核心指标，表示有效信号功率与噪声功率之比。SNR 越高，说明信号中噪声污染越少，质量越好。

在图像处理领域，SNR 用于量化图像中有效信息与噪声干扰的相对强度，常用于评估图像采集、压缩、去噪等处理步骤的质量。

---

## 2. 数学表达

### 2.1 线性比值形式

$$
\text{SNR} = \frac{P_{\text{signal}}}{P_{\text{noise}}}
$$

### 2.2 分贝（dB）形式

$$
\text{SNR}_{\text{dB}} = 10 \log_{10}\!\left(\frac{P_{\text{signal}}}{P_{\text{noise}}}\right)
$$

其中 $P_{\text{signal}}$ 为信号功率（能量），$P_{\text{noise}}$ 为噪声功率（能量）。

---

## 3. 图像场景下"信号"与"噪声"的定义

| 方法 | 信号 $P_{\text{signal}}$ | 噪声 $P_{\text{noise}}$ |
|---|---|---|
| **参考图像 vs 噪声图像** | 参考（无噪）图像 $I$ 的平均功率 | 噪声分量 $n = \hat{I} - I$ 的平均功率 |
| **均值/方差定义** | 图像像素均值的平方 $\mu^2$ | 图像像素方差 $\sigma^2$ |
| **误差定义** | 原始图像 $I$ 的平均功率 | 重建误差 $e = I - \hat{I}$ 的平均功率 |

> 其中 $I$ 为参考（理想/无噪）图像，$\hat{I}$ 为含噪或重建图像。

---

## 4. 常见计算公式

设图像共有 $N$ 个像素：

### 4.1 基于参考图像与误差

$$
P_{\text{signal}} = \frac{1}{N} \sum_{i=1}^{N} I_i^2 = \text{mean}(I^2)
$$

$$
P_{\text{noise}} = \frac{1}{N} \sum_{i=1}^{N} (I_i - \hat{I}_i)^2 = \text{mean}\!\left((I - \hat{I})^2\right)
$$

$$
\text{SNR} = \frac{P_{\text{signal}}}{P_{\text{noise}}} = \frac{\text{mean}(I^2)}{\text{mean}((I-\hat{I})^2)}
$$

$$
\text{SNR}_{\text{dB}} = 10 \log_{10}\!\left(\frac{\text{mean}(I^2)}{\text{mean}((I-\hat{I})^2)}\right)
$$

### 4.2 Python 示例

```python
import numpy as np

def snr(reference: np.ndarray, noisy: np.ndarray) -> float:
    """计算信噪比（dB）。
    
    Args:
        reference: 参考（无噪）图像，浮点数组。
        noisy:     含噪或重建图像，与 reference 形状相同。
    
    Returns:
        SNR 值（dB）。
    """
    signal_power = np.mean(reference ** 2)
    noise_power  = np.mean((reference - noisy) ** 2)
    return 10 * np.log10(signal_power / noise_power)
```

---

## 5. SNR 与 PSNR 的区别与联系

### 5.1 PSNR（峰值信噪比，Peak Signal-to-Noise Ratio）

PSNR 将分子中的信号功率替换为像素最大值的平方（即"峰值功率"）：

$$
\text{PSNR} = 10 \log_{10}\!\left(\frac{\text{MAX}^2}{\text{MSE}}\right)
$$

其中：

- $\text{MAX}$ 为像素的最大取值（如 8-bit 图像取 255）；
- $\text{MSE} = \dfrac{1}{N}\displaystyle\sum_{i=1}^{N}(I_i - \hat{I}_i)^2$ 为均方误差。

### 5.2 对比总结

| 指标 | 分子（信号参考） | 适用场景 |
|---|---|---|
| **SNR** | 原始信号自身的平均功率 $\text{mean}(I^2)$ | 信号幅度变化大、需归一化到实际信号强度时 |
| **PSNR** | 峰值功率 $\text{MAX}^2$（固定常数） | 图像压缩、编解码质量评估；便于跨图像横向比较 |

- **SNR** 会随图像本身亮度/能量变化而变化，更能反映实际信号质量。  
- **PSNR** 分子固定为峰值功率（与图像内容无关），计算简单，是图像/视频编解码领域的行业标准指标，但对低亮度图像可能偏高。  
- 两者均不能完全反映人眼的主观感受，实际应用中常与 **SSIM**（结构相似性）等感知指标联合使用。

---

## 6. 参考

- [Wikipedia: Signal-to-noise ratio](https://en.wikipedia.org/wiki/Signal-to-noise_ratio)  
- [Wikipedia: Peak signal-to-noise ratio](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)

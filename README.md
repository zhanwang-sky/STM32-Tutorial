# STM32-Tutorial

## HAL

### 初始化

1. 初始化HAL
```
HAL_Init()
  // __HAL_FLASH_INSTRUCTION_CACHE_DISABLE()
  // __HAL_FLASH_DATA_CACHE_DISABLE()
  __HAL_FLASH_PREFETCH_BUFFER_ENABLE()
  HAL_NVIC_SetPriorityGrouping(NVIC_PRIORITYGROUP_4) // 全抢占
  __weak HAL_InitTick(TICK_INT_PRIORITY)
    // ro: SystemCoreClock (CMSIS)
    // ro: uwTickFreq
    // wo: uwTickPrio
    HAL_SYSTICK_Config()
    HAL_NVIC_SetPriority(SysTick_IRQn, ...)
  __weak HAL_MspInit()
```

注意：配置其他功能前，先开启SYSCFG和PWR的时钟。
```
__HAL_RCC_SYSCFG_CLK_ENABLE()
__HAL_RCC_PWR_CLK_ENABLE()
```

2. 配置内部稳压器。注意：稳压值关乎CPU能达到的最高频率，必须正确配置。
```
// PWR_REGULATOR_VOLTAGE_SCALE1_BOOST: 1.28V, up to 170 MHz
// PWR_REGULATOR_VOLTAGE_SCALE1: 1.2V, up to 150 MHz
// PWR_REGULATOR_VOLTAGE_SCALE2: 1.0V, up to 26 MHz
HAL_PWREx_ControlVoltageScaling(PWR_REGULATOR_VOLTAGE_SCALE1_BOOST)
```

3. 配置振荡器与锁相环。注意：参考STM32Cube配置。
```
HAL_RCC_OscConfig()
```

4. 配置总线时钟。注意：CPU频率必须在稳压器允许的范围内；FLatency必须足够长以便能正确读取数据。
```
HAL_RCC_ClockConfig(_, FLASH_LATENCY_4)
  // wo: SystemCoreClock (CMSIS)
  HAL_InitTick(uwTickPrio)
```

5. 重写`HAL_MspInit()`函数
```
HAL_MspInit()
  // HAL_NVIC_SetPriorityGrouping()
```

6. 重写`HAL_InitTick()`函数
```
HAL_InitTick()
  // target_frequency = 1000 / uwTickFreq
  // save uwTickPrio
```

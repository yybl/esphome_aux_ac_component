# ESPHome AUX空调定制组件（aux_ac） #
关于本项目的沟通 [请加入这个telegram chat](https://t.me/aux_ac). 
 
有关问题或功能请求，请转到 [the issue section](https://github.com/GrKoR/esphome_aux_ac_component/issues). It will be perfect if you attach log to your issue. Log you can collect with [this python script](https://github.com/GrKoR/ac_python_logger). It helps you to save all data frames from the UART bus to a csv-file. This log combined with the detailed situation description will significantly speed up bug correction.
 
 
## 免责声明 ##
1. 本项目的所有数据（软件、固件、方案、三维模型等） 均为 **'原样提供'**. 使用它对你的设备所做的一切,都将由你承担一切风险.如果你不明白你在做怎么, 请直接购买空调制造商的WIFI模块.
2. I am not a programmer. So source code is certainly not optimal and badly decorated (but there are a lot of comments in it; sorry, a significant part of it is in Russian). Also, code may be written unsafe. I tried to test all parts of the code, but I'm sure I missed a lot of things. So treat it with suspicion, expect a trick from it, and if you discover something wrong write an issue here.
3. Russian and English readme files are substantially identical in meaning. But in case of differences, the [Russian](https://github.com/GrKoR/esphome_aux_ac_component#readme) version is more significant.
 
## Short description ##
This custom component allows you to control your air conditioner through wifi if it is made in the AUX factory.<br />
Component tested with ESPHome 1.18.0 and Rovex ALS1 air conditioner. It looks like many other air conditioners can be controlled by `aux_ac`, but this possibility isn't tested. See list of tested ACs below for more details.
 
 
## Supported air conditioners ##
AUX is one of the OEM air conditioner manufacturers. AUX produce ACs for many brands.
There is the following list of AUX-based air conditioner on the internet: AUX, Abion, AC ELECTRIC, Almacom, Ballu, Centek, Climer, DAX, Energolux, ERISSON, Green Energy, Hyundai, IGC, Kentatsu (some series only), Klimaire, KOMANCHI, LANZKRAFT, LEBERG, LGen, Monroe, Neoclima, NEOLINE, One Air, Pioneer (until 2016), Roda, Rovex, Royal Clima, SAKATA, Samurai, SATURN, Scarlett, SmartWay, Soling, Subtropic, SUBTROPIC, Supra, Timberk, Vertex, Zanussi. There are doubts about its completeness and reliability, but nothing better could be found.

### List of compatible ACs (tested) ###
[The list of tested ACs](docs/AC_TESTED.md) is placed in a separate file and includes tested by the author or by users ACs. This list is permanently updated, mainly based on feedback from users in [Telegram chat](https://t.me/aux_ac).<br />

If your AC is not in the list
If your AC is listed above you should take a closer look at aux_ac.
If the User Manual of your HVAC describes connection to wifi with mobile app ACFreedom it seems you may go deeper with aux_ac. But try all soft and hardware for your own risk. You must clearly understand what you are doing.
If you are unsure it is better to wait while other users will test your model of AC (but it may never). Or please go to telegram-chat with your questions. Maybe you will get help there.

If you have tested your air conditioner and aux_ac works with it please let me know about it. I'll add this info to the list of tested ACs above. The best way to report about your test results is write a message in the telegram or in the issue section.

How to use it
For correct component operation you need hardware and firmware. The hardware description is located in separate file.

Firmware: Integration aux_ac to your configuration
You need ESPHome v.1.18.0 or above. External_components have appeared in this version. But it is better to use ESPHome v.1.20.4 or above cause there was alot of external_components errors corrected before this version.
### If your AC is not in the list ###
If your AC is listed above, you should take a closer look at `aux_ac`.<br />
If the User Manual of your HVAC describes connection to wifi with mobile app ACFreedom it seems you may go deeper with `aux_ac`. But try all soft and hardware for your own risk. You must clearly understand what you are doing.<br />
If you are unsure, it is better to wait while other users will test your model of AC (but it may never). Or please [go to telegram-chat](https://t.me/aux_ac) with your questions. Maybe you will get help there.

If you have tested your air conditioner and `aux_ac` works with it, please let me know about it. I'll add this info to the list of tested ACs above.
The best way to report about your test results is writing a message in the [telegram](https://t.me/aux_ac) or [in the issue section](https://github.com/GrKoR/esphome_aux_ac_component/issues).


## How to use it ##
For correct component operation, you need hardware and firmware. The hardware description is located [in a separate file](docs/HARDWARE-EN.md).

### 固件: 将aux_ac集成到您的配置 ###
你需要 [ESPHome](https://esphome.io) v.1.18.0 或更高版本, 此版本中有`External_components` . 但最好使用 ESPHome v.1.20.4 或更高版本, 因为在此版本之前有很多`external_components` 的错误已经修正.

## 安装 ##
1. external component声明. 请查看这个 [手册](https://esphome.io/components/external_components.html?highlight=external) 获取更多信息.
```yaml
external_components:
  - source:
      type: git
      url: https://github.com/GrKoR/esphome_aux_ac_component
```
2. 配置UART口与空调通讯:
```yaml
uart:
  id: ac_uart_bus
  # 注意! 有些NodeMCU开发板使用GPIO4 (D2) 和 GPIO5 (D1) 作为 TX 和 RX 的通讯口 !
  # 详细信息请查看文档: https://github.com/GrKoR/esphome_aux_ac_component/blob/master/docs/HARDWARE-EN.md
  tx_pin: GPIO1
  rx_pin: GPIO3
  baud_rate: 4800
  data_bits: 8
  parity: EVEN
  stop_bits: 1
```
3. **注意!** 你需要禁用ESPHome的logger,防止它把数据发送给空调. 从UART接口禁用logger不会影响到控制台（console）或者web服务器的logger输出.
```yaml
logger:
    baud_rate: 0
```
如果出于某些原因你需要将logger输出至 】UART接口, 你可以切换到另一个UART接口. ESP8266支持两个硬件UART接口: UART0 和 UART1. 仅UART0支持`aux_ac`，因为它同时具有TX和RX. UART1 仅支持TX, 可以用于logger的输出:
```yaml
logger:
    level: DEBUG
    hardware_uart: UART1
```

## AUX_AC 配置 ##
最小配置:
```yaml
climate:
  - platform: aux_ac
    name: "AC Name"
```

完整配置:
```yaml
climate:
  - platform: aux_ac
    name: "AC Name"
    id: aux_id
    uart_id: ac_uart_bus
    period: 7s
    show_action: true
    display_inverted: false
    timeout: 150
    indoor_temperature:
      name: AC Indoor Temperature
      id: ac_indoor_temp
      accuracy_decimals: 1
      internal: false
    outdoor_temperature:
      name: AC Outdoor Temperature
      id: ac_outdoor_temp
      internal: false
    outbound_temperature:
      name: AC Colant Outbound Temperature
      id: ac_outbound_temp
      internal: false
    inbound_temperature:
      name: AC Colant Inbound Temperature
      id: ac_inbound_temp
      internal: false
    compressor_temperature:
      name: AC Compressor Temperature
      id: ac_strange_temp
      internal: false
    display_state:
      name: AC Display State
      id: ac_display_state
      internal: false
    defrost_state:
      name: AC Defrost State
      id: ac_defrost_state
      internal: false
    inverter_power:
      name: AC Inverter Power
      id: ac_inverter_power
      internal: false
    inverter_power_limit_value:
      name: AC Inverter Power Limit Value
      id: ac_inverter_power_limit_value
      internal: false
    inverter_power_limit_state:
      name: AC Inverter Power Limit State
      id: ac_inverter_power_limit_state
      internal: false
    preset_reporter:
      name: AC Preset Reporter
      id: ac_preset_reporter
      internal: false
    vlouver_state:
      name: AC Vertical Louvers State
      id: ac_vlouver_state
      internal: false
    visual:
      min_temperature: 16
      max_temperature: 32
      temperature_step: 1
    supported_modes:
      - HEAT_COOL
      - COOL
      - HEAT
      - DRY
      - FAN_ONLY
    custom_fan_modes:
      - MUTE
      - TURBO
    supported_presets:
      - SLEEP
    custom_presets:
      - CLEAN
      - HEALTH
      - ANTIFUNGUS
    supported_swing_modes:
      - VERTICAL
      - HORIZONTAL
      - BOTH
```

## 配置变量: ##

- **name** (**必须**, string): 空调设备的名称.  `id` 或 `name` 至少要填一个!

- **id** (*可选*, [ID](https://esphome.io/guides/configuration-types.html#config-id)): 手动指定用于代码的ID.  `id`或`name`至少要填一个!

- **uart_id** (*可选*, [ID](https://esphome.io/guides/configuration-types.html#config-id)): 手动指定[UART Bus](https://esphome.io/components/uart.html) 的ID，如果你打算使用多个UART总线.

- **period** (*可选*, [time](https://esphome.io/guides/configuration-types.html#config-time), 默认 ``7s``): 向AC发送状态请求的间隔时间. 即便你使用红外遥控器更改空调的状态，`Aux_ac` 也仅在定期发出请求后才会收到空调状态.

- **show_action** (*可选*, boolean, default ``true``): 是否显示设备当前的动作 (实验). 例如, 在（HEAT_COOL）模式下, 空调硬件可能属于以下动作中:
  - HEATING: 空调正在加热房间;
  - IDLE: 因已达到目标温度，空调正工作在风扇（FAN）模式;
  - COOLING: 空调正在制冷.
  在 HEAT（加热）或 COOL（制冷）模式下也是如此, 唯一不同的是动作列表里显示 (IDLE + HEATING 或 IDLE + COOLING).

  - **display_inverted** (*可选*, boolean, default ``false``): It configures display driver logic level. As it turned out in the issue [#31](https://github.com/GrKoR/esphome_aux_ac_component/issues/31), different models of conditioners manage display different way. Rovex ACs powers off display by bit `1` in command packet and power it on by bit `0`. Many other conditioners do this vice versa.

- **timeout** (*可选*, unsigned integer, default ``150``):  `aux_ac`接收数据包的超时时间.  
  通常无需修改此值，默认即可.  
  唯一需要修改此值的情况是ESP任务繁重. 当你使用ESP执行过多超负荷的任务时, `aux_ac`可能没有足够的时间去接收 AC 发送的数据包. 在这种情况下可以稍微提高超时时间，但最好的方案是从ESP中删除一些任务.  
  超时限制在 `150` 至 `600` 毫秒内. 只有修改源代码才能设定更大的范围，但我不建议那样做.

- **indoor_temperature** (*可选*): 内部温度传感器参数.
  - **name** (**Required**, string): 这个温度传感器的名称.
  - **id** (*Optional*, [ID](https://esphome.io/guides/configuration-types.html#config-id)): 设置 ID 用于这个传感器的lambdas表达式.
  - **internal** (*Optional*, boolean): 将此组件标记为内部组件. Internal components will not be exposed to the frontend (like Home Assistant). As opposed to default [Sensor](https://esphome.io/components/sensor/index.html#base-sensor-configuration) behaviour, this variable is **always true** except in cases where the user has set it directly.
  - All other options from [Sensor](https://esphome.io/components/sensor/index.html#base-sensor-configuration).

- **outdoor_temperature** (*可选*): 外部温度传感器参数. They are the same as the **indoor_temperature** (see description above).  
  > **注意!** 当空调关闭时, 外部温度传感器很少更新 (每 6-7 小时更新一次). 这不是组件的bug, 而是空调设备的一个机制. 要更频繁的更新，唯一的办法就是创建一个模板传感器（template sensor）, 对这个温度进行更新. 当空调工作时, 使用 **outdoor_temperature**更新模板传感器（template sensor）的值. 在空调关闭时, 应根据 **outbound_temperature** 传感器的动态特征重新计算此温度的值 (当空调关闭时它频繁变化，并接近空气温度值). 你不应直接复制 **outbound_temperature** 的值去更新模板传感器，它们并不完全一样（template sensor），（You can't copy the value of **outbound_temperature** without changes to the template sensor in AC off mode, because these temperatures are not identical.）

- **inbound_temperature** (*Optional*): 冷却剂入口的温度传感器参数. 它们与 **indoor_temperature** 相同(请参见上面的描述).

- **outbound_temperature** (*Optional*):  冷却剂出口的温度传感器参数. 它们与 **indoor_temperature** 相同(请参见上面的描述).

- **compressor_temperature** (*Optional*):  压缩机温度传感器参数. 它们与 **indoor_temperature** 相同(请参见上面的描述).

- **display_state** (*Optional*): The information for the HVAC display state sensor (is display ON or OFF)
  - **name** (**Required**, string): The name for the display state sensor.
  - **id** (*Optional*, [ID](https://esphome.io/guides/configuration-types.html#config-id)): Set the ID of this sensor for use in lambdas.
  - **internal** (*Optional*, boolean): Mark this component as internal. Internal components will not be exposed to the frontend (like Home Assistant). As opposed to default [Binary Sensor](https://esphome.io/components/binary_sensor/index.html#base-binary-sensor-configuration) behavior, this variable is **always true** except in cases where the user has set it directly.
  - All other options from [Binary Sensor](https://esphome.io/components/binary_sensor/index.html#base-binary-sensor-configuration).

- **defrost_state** (*Optional*): The information for the HVAC defrost function state sensor (is it ON or OFF). All settings are the same as for the **display_state** (see description above).

- **inverter_power** (*Optional*): The information for the inverter power sensor. All settings are the same as for the **indoor_temperature** (see description above).
  > **ATTENTION!** The parameter name was changed in v.0.2.9 due to incorrect spelling.

- **inverter_power_limit_state** (*Optional*): Configuration of the power limit state sensor. It displays the state of the power limitation function for the inverter HVAC (is it ON or OFF). All settings are the same as for the **display_state** (see description above).

- **inverter_power_limit_value** (*Optional*): Configuration of the power limit value sensor. All settings are the same as for the **indoor_temperature** (see description above).  
It reports the current value of the power limitation function for the inverter HVAC. This sensor represents the value only after the HVAC confirms the power limitation. The value is always in the range from 30 to 100%. This is the hardware limitation.

- **preset_reporter** (*Optional*): Parameters of text sensor with current preset. All settings are the same as for the **display_state** (see description above).  
  ESPHome Climate devices are not reporting their active presets (from **supported_presets** and **custom_presets** lists) to MQTT. This behavior has been noticed at least in version 1.20.0. In case you are using MQTT and want to receive information about active preset, you should declare this sensor in your yaml.

- **vlouver_state** (*Optional*): Parameters of vertical louvers state sensor. All settings are the same as for the **display_state** (see description above). The state of the vertical louvers is encoded by the integer value (see [aux_ac.vlouver_set action](#aux_ac_._vlouver_set) below).

- **supported_modes** (*Optional*, list): List of supported modes. Possible values are: ``HEAT_COOL``, ``COOL``, ``HEAT``, ``DRY``, ``FAN_ONLY``. Please note: some manufacturers call AUTO mode instead of HEAT_COOL. Defaults to ``FAN_ONLY``.

- **custom_fan_modes** (*Optional*, list): List of supported custom fan modes. Possible values are: ``MUTE``, ``TURBO``. No custom fan modes by default.

- **supported_presets** (*Optional*, list): List of supported presets. Possible values are: ``SLEEP``. No presets by default.

- **custom_presets** (*Optional*, list): List of supported custom presets. Possible values are: ``CLEAN``, ``HEALTH``, ``ANTIFUNGUS``. No custom presets by default.

- **supported_swing_modes** (*Optional*, list): List of supported swing modes. Possible values are: ``VERTICAL``, ``HORIZONTAL``, ``BOTH``. No swing modes by default.

- All other options from [Climate](https://esphome.io/components/climate/index.html#base-climate-configuration).


## Actions: ##
### ``aux_ac.display_on`` ###
This action turns a HVAC temperature display on when executed.

```yaml
on_...:
  then:
    - aux_ac.display_on: aux_id
```
- **aux_id** (**Requared**, string): ID of `aux_ac` component.

### ``aux_ac.display_off`` ###
This action turns a HVAC temperature display off when executed.

```yaml
on_...:
  then:
    - aux_ac.display_off: aux_id
```
- **aux_id** (**Requared**, string): ID of `aux_ac` component.

### ``aux_ac.vlouver_set`` ###
This action moves HVAC vertical louvers to the specified position.

The position is encoded by the following values:  
- `0`: the vertical louvers are in `SWING` mode (they are moving up and down);
- `1`: the louvers are stopped in a user position;
- `2`: the louvers are in the topmost position;
- `3`: the louvers are in one step above middle position;
- `4`: the louvers are in the middle position;
- `5`: the louvers are in one step below middle position;
- `6`: the louvers are in the lowest position.

```yaml
on_...:
  then:
    - aux_ac.vlouver_set:
        id: aux_id
        position: 3 # moves the louvers to the middle position
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.
- **position** (**Required**, integer): position of the vertical louvers.

### ``aux_ac.vlouver_stop`` ###
This action stops vertical swing of louvers.

```yaml
on_...:
  then:
    - aux_ac.vlouver_stop: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.vlouver_swing`` ###
This action starts the vertical swing of louvers.

```yaml
on_...:
  then:
    - aux_ac.vlouver_swing: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.vlouver_top`` ###
This action moves HVAC louvers to the topmost position.

```yaml
on_...:
  then:
    - aux_ac.vlouver_top: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.vlouver_middle_above`` ###
This action moves HVAC louvers to the position one step under the topmost.

```yaml
on_...:
  then:
    - aux_ac.vlouver_middle_above: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.vlouver_middle`` ###
This action moves HVAC louvers to the middle position.

```yaml
on_...:
  then:
    - aux_ac.vlouver_middle: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.vlouver_middle_below`` ###
This action moves HVAC louvers to the position one step under the middle position.

```yaml
on_...:
  then:
    - aux_ac.vlouver_middle_below: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.vlouver_bottom`` ###
This action moves HVAC louvers to the lowest position.

```yaml
on_...:
  then:
    - aux_ac.vlouver_bottom: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.aux_ac.power_limit_off`` ###
This action disables inverter HVAC power limitation.

```yaml
on_...:
  then:
    - aux_ac.power_limit_off: aux_id
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.

### ``aux_ac.power_limit_on`` ###
This action enables inverter HVAC power limitation and sets this limit value.

```yaml
on_...:
  then:
    - aux_ac.power_limit_on:
        id: aux_id
        limit: 46   # limits the maximum power of the inverter HVAC at 46%
```
- **aux_id** (**Required**, string): ID of `aux_ac` component.
- **limit** (**Optional**, integer): the maximum power of the inverter HVAC. If the power limitation is enabled, the inverter HVAC will limits its power.  
 > **Notice**, that power limitation will affect the efficiency of your HVAC. For example, a low power limit may block the possibility of the conditioner to reach user-specified room temperature, because HVAC will not have enough power for it. Keep this in mind when you are using this function.
  
 Due to hardware limitation this value should be in the range from `30%` to `100%`. The default value for `limit` is `30%` (it will be used if `limit` is omitted in configuration).



## Simple example ##
The source code of this example is located in the [aux_ac_simple.yaml](https://github.com/GrKoR/esphome_aux_ac_component/blob/master/examples/simple/aux_ac_simple.yaml) file.

All settings in it is trivial. Just copy the file to your local folder, specify your wifi settings and compile YAML with ESPHome.


## Advanced example ##
All sources are located [in advanced example folder](https://github.com/GrKoR/esphome_aux_ac_component/tree/master/examples/advanced).

This time we'll configure two relative identical air conditioners with `aux_ac` custom component.<br />
Let's imagine we have ACs in a kitchen and in a living room. All ACs are the same brand and can be controlled by `aux_ac`.<br />  
Because we are lazy, we'll define all common configuration parts for two air conditioners in one `ac_common.yaml` file.<br />
All specific parts of configuration are located in the `ac_kitchen.yaml` and `ac_livingroom.yaml`. Here we set `devicename` and `upper_devicename` for correct sensors and component naming. And here we specify the correct IP-address of the device from `secrets.yaml`.<br />
**Don't forget** to specify `wifi_ip_kitchen`, `wifi_ota_ip_kitchen`, `wifi_ip_livingroom` and `wifi_ota_ip_livingroom` in the `secrets.yaml` along with the other sensitive information, such as passwords, tokens etc.

If you try to compile `ac_common.yaml` it will raise errors. You need to compile `ac_kitchen.yaml` or `ac_livingroom.yaml` instead.

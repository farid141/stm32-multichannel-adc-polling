# stm32-multichannel-adc-polling
adc sampling polling conversion mode. You can find main.c file which is core code for this project. I've make 2 function that can be used to read adc and convert adc to voltage

## getADCAvg
get average adc value on specified adc channel  
this function takes 3 argument:
channel: adc channel macros on HAL API (ex: ADC_CHANNEL_0, ADC_CHANNEL_1, etc)
sample: sample number of adc read
hadc: pointer of ADC_HandleTypeDef
```C
float getADCAvg(uint32_t channel, int sample, ADC_HandleTypeDef* hadc)
{
	/*
	 * channel = ADC_CHANNEL_0
	 */
    //KONFIGURASI ADC
    ADC_ChannelConfTypeDef sConfig = {0};
    sConfig.Channel = channel;
    sConfig.Rank = 1;
    sConfig.SamplingTime = ADC_SAMPLETIME_1CYCLE_5;
    if (HAL_ADC_ConfigChannel(hadc, &sConfig) != HAL_OK)
    {
        Error_Handler();
    }
    HAL_ADCEx_Calibration_Start(hadc);

    //SAMPLING ADC
    float adcVal = 0;
    for (int i = 0; i < sample; i++)
    {
        HAL_ADC_Start(hadc);
        HAL_ADC_PollForConversion(hadc, 100);
        adcVal += HAL_ADC_GetValue(hadc);
    }
    adcVal /= sample;
    return adcVal;
}
```

### function call example
```C
getADCAvg(ADC_CHANNEL_0, 10, &hadc1);
```

## adcToVolt
convert adc value of STM32F103x to volt  
this function takes 1 argument:
hadc: adc value
```C
float adcToVolt(float adc){
	return (adc/4095.0)*3.21;
}
```

### function call example
```C
adcToVolt(4095);
```

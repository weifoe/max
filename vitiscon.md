# vitis 操作流程

## 1. create platform component (Name and Location)

 ![image](https://github.com/user-attachments/assets/f2818b9f-7b3d-46ee-8ec5-19f7199f0074)


### >設定好文件名字和資料夾

## 2. create platform component (Flow) 

![image](https://github.com/user-attachments/assets/d1241abb-6909-4bad-b63c-8e9aea0269a4)




### >引入xsa檔案

## 3. create platform component (OS and Processor)

![image](https://github.com/user-attachments/assets/36ceaec5-17fb-4b49-9ebd-c4b1dfb2977b)



### >設定處理器

## 4.create platform component (Summary)
![image](https://github.com/user-attachments/assets/811b2e3b-6088-4cb4-afc9-7b4584b5ea00)


### >finish

---
## 1. Empty Application

![image](https://github.com/user-attachments/assets/b2686dd1-bd99-421c-917f-fec473878ae7)


### >創建 Empty Application

## 2. Create Application Component (Name and Location)

![image](https://github.com/user-attachments/assets/6ba11999-e821-4c68-9777-d44c8b9a3458)


### >創建文件

## 3. Create Application Component (Hardware)

![image](https://github.com/user-attachments/assets/932594fe-07bc-4823-ad4e-b0751f046be6)



### >Select Platform(ego-xz7)


## 4. Create Application Component (Domain)

![image](https://github.com/user-attachments/assets/b4a995ea-d54f-4e93-9330-1f590b179900)


### > 選擇standalone_microblaze_riscv_0

## 5. Create Application Component (Source File)

![image](https://github.com/user-attachments/assets/4fd5b658-b975-44aa-97be-8cf9b8c9b2f0)



### > Next

## 6. Create Application Component (Summary)

![image](https://github.com/user-attachments/assets/010341ee-0a54-435a-b53e-da6ee5676673)


### > Finish

---

# 編譯C程式碼

## 1. Build platform

![image](https://github.com/user-attachments/assets/da0fa002-bccb-44cc-8898-1ba321faef3c)



## 1.1 build platform 成功

### output 畫面確認build成功

![image](https://github.com/user-attachments/assets/3d2843e3-20d8-435b-9184-163536d300df)



### 確認 empty_application的include資料夾有產生.h檔案

![image](https://github.com/user-attachments/assets/09d9e63e-aec6-4c7b-910f-f962d234794d)



## 2 empty_application產生.c檔案

### 對src按右鍵->New File->輸入name.c(本說明手冊由於已產生hello.c所以已name.c舉例,以下說明都已hello.c為實作範例)->ok


![image](https://github.com/user-attachments/assets/42d05556-301b-4267-8793-2e2c9f6100f8)


### 3 編寫 hello.c 

#### > 測試程式碼


```c
#include "xgpio.h"

#include "xparameters.h"

#define XPAR_AXI_GPIO_0_DEVICE_ID 0

#define GPIO_DEVICE_ID XPAR_AXI_GPIO_0_DEVICE_ID

int main() {

    XGpio Gpio;

    int status;

    int value = 0xAA;  // 初始燈光狀態（10101010）

    status = XGpio_Initialize(&Gpio, GPIO_DEVICE_ID);

    if (status != XST_SUCCESS) {
        return XST_FAILURE;
    }

    XGpio_SetDataDirection(&Gpio, 1, 0x00);  // 通道1為輸出

    while (1) {
        XGpio_DiscreteWrite(&Gpio, 1, value);  // 輸出當前燈光狀態
        value = ~value;                         // 將燈光狀態反轉，達到閃爍效果
        for (volatile int i = 0; i < 10000000; i++);  // 簡單延遲，視頻率調整
    }

    return 0;
}
```

### 3-1 將以上程式碼貼到hello.c

![image](https://github.com/user-attachments/assets/9f94bbde-856d-4d70-9241-af863da17c58)



### > 對 empty_application執行build

![image](https://github.com/user-attachments/assets/27a64fea-621e-4d59-b995-e23fc2fab2f0)



### > 確認有產生.elf檔案(很重要)

![image](https://github.com/user-attachments/assets/4c4a92a9-eca1-47e4-9473-aa5342317e62)


### >按下run(右下角出現錯誤正常))

![image](https://github.com/user-attachments/assets/135bf156-9dd9-4a4c-8c35-67615132311d)


### > 按下run確認板子有亮藍燈

![image](https://github.com/user-attachments/assets/197936b9-3024-4787-8740-7cd03a8e3a25)


### 3-2 啟動xsdb將.elf檔案載入到板子

![image](https://github.com/user-attachments/assets/37d734b9-abfd-4cd4-a233-427c8af65c40)


> vitis->XSDB console

#### XSDB 指令

```
xsdb% connect

```
![image](https://github.com/user-attachments/assets/235bc1ee-774e-4e29-8625-21cdf6673123)


```

xsdb% targets

```

![image](https://github.com/user-attachments/assets/5c6af8d3-d923-4d7e-a250-8c9b2bd02698)




```
xsdb% targets 7
xsdb% dow D:/xinlinx_205/project_3/newrisc(此專案資料夾名字)/empty_application/build/empty_application.elf(基本上elf檔案都在empty_application/build底下)

```
## 以上指令執行後

## 結果會像此影片

https://youtu.be/5ZMqT0NsJ04


---


### 1 單元測試(AXI IP連接))

![image](https://github.com/user-attachments/assets/efdfd3b6-74a3-43a5-9de6-31ee75268b33)

### > 紅框為自定義的AXI IP



### 說明:利用microblaze v連接自定義AXI IP,AXI IP接收到資料啟動LED燈


### 載入elf檔案到FPGA的RISCV核心(畫面為XSDB指令畫面)

![image](https://github.com/user-attachments/assets/1c07677c-2779-4c36-8f42-e4876fae495b)

### > 紅框為ELF檔案載入到RISCV核心編譯C程式


### Demo結果

![133322](https://github.com/user-attachments/assets/90d90ff2-de8d-4923-bca3-f59fa9ee71ff)







           




































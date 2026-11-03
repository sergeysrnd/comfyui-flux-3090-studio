# Установка

## Проверенная конфигурация

- Windows, ComfyUI Desktop **1.1.8**.
- ComfyUI **0.43.2**, frontend **1.57.4**, **Nodes 2.0**.
- RTX 3090 **24 ГБ VRAM**, **32 ГБ RAM**.
- Python **3.13**, PyTorch **2.12.1+cu130**.

Это запись о проверенной установке, а не минимальные требования.
Совместимость со старыми версиями frontend/subgraphs отдельно не проверялась.
Схема использует штатные ноды; custom nodes и отдельные Python-пакеты не нужны.

## 1. Скачать файлы репозитория

На GitHub нажми **Code → Download ZIP**, распакуй архив.
Главный файл — `FLUX_3090_STUDIO.json` в корне репозитория.
Его можно скачать отдельно; `examples`, `docs` и `prompts` содержат примеры и документацию.

## 2. Разложить модели

Пути ниже указаны относительно папки данных твоей установки ComfyUI.
В Desktop это может быть общая папка `ComfyUI-Shared`, а не каталог с `.exe`.

```text
models/
├── diffusion_models/
│   └── flux1-dev.safetensors
├── text_encoders/
│   ├── clip_l.safetensors
│   └── t5xxl_fp16.safetensors
├── vae/
│   └── ae.safetensors
└── upscale_models/
    └── RealESRGAN_x2plus.pth
```

| Файл | Источник |
| --- | --- |
| flux1-dev.safetensors | [Comfy-Org / FLUX.1-dev](https://huggingface.co/Comfy-Org/Flux-1-Dev) — нужен отдельный diffusion-файл |
| clip_l.safetensors | [flux_text_encoders / CLIP-L](https://huggingface.co/comfyanonymous/flux_text_encoders/blob/main/clip_l.safetensors) |
| t5xxl_fp16.safetensors | [flux_text_encoders / T5 FP16](https://huggingface.co/comfyanonymous/flux_text_encoders/blob/main/t5xxl_fp16.safetensors) |
| ae.safetensors | [Официальная инструкция ComfyUI со ссылкой на VAE](https://docs.comfy.org/tutorials/flux/flux-1-text-to-image) |
| RealESRGAN_x2plus.pth | [Официальный релиз Real-ESRGAN v0.2.1](https://github.com/xinntao/Real-ESRGAN/releases/tag/v0.2.1) — файл около 67 МБ |

Для FLUX используется исходный `flux1-dev.safetensors` с загрузкой весов в
**fp8_e4m3fn**. Это настройка Load Diffusion Model внутри subgraph.
Файл на диске не переписывается. T5 остаётся FP16; ComfyUI управляет его
размещением и выгрузкой. Проверка на 3090 прошла с этой конфигурацией.

`flux1-dev-fp8.safetensors` как единый checkpoint — другой вариант упаковки.
Эта схема ожидает отдельно diffusion model, два text encoder и VAE.
Поэтому сохрани приведённые имена и расположение файлов.

После добавления моделей перезапусти ComfyUI либо обнови определения нод через
**ComfyUI → Edit → Refresh Node Definitions**. При проверке этого хватило, чтобы
новая модель увеличения появилась в списке frontend.

## 3. Открыть один workflow

1. Перетащи `FLUX_3090_STUDIO.json` из Проводника на холст ComfyUI.
   Либо выбери **меню ComfyUI → File → Open** и укажи этот файл.
2. Сохрани схему через **Graph → Save**. Внутри уже есть оба этапа.
3. В меню ComfyUI включи **Nodes 2.0** для штатного сравнения A/B.
4. На новом ПК перед первым Run выбери любой PNG через значок папки в средней
   верхней ноде. Можно использовать `examples/FLUX_3090_selected_lighthouse.png`.
   ComfyUI проверяет наличие входного файла даже при выключенном FINISH.
   В исходной проверенной установке файл с маяком уже находится в input.
5. **ЭТАП OFF** — поиск кадра, **ЭТАП ON** — обработка выбранного PNG.
6. Выполни [пошаговый пример](guide.md).

Веса FLUX не помещаются целиком вместе со всеми компонентами в VRAM этой карты;
управление памятью и загрузка весов могут влиять на паузы первого запуска.
Оставь batch 1 и начни с проверенного размера 1344 × 768.

## Лицензии моделей

Модели скачиваются из указанных источников отдельно и используются на их условиях.
У FLUX.1-dev есть [собственная лицензия и условия](https://huggingface.co/black-forest-labs/FLUX.1-dev).
Лицензия MIT этого репозитория относится к авторским схемам и документации.

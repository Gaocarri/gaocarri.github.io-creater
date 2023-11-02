---
# 常用定义
title: "手撸一个批量生成图片并打包成zip的组件" # 标题
date: 2023-10-13 # 创建时间
draft: false # 是否是草稿？
tags: ["JavaScript", "React"] # 标签
categories: ["JavaScript"] # 分类
author: "Carri" # 作者
keywords: ["JavaScript", "React"]
description: "手撸一个批量生成图片并打包成zip的组件"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true # 关闭评论
toc: true # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false # 关闭打赏
mathjax: true # 打开 mathjax
---

接到一个需求，需要在点击导出按钮后，根据这些组件对应的 DOM 结构，生成一组图片，并将图片打包成一个 zip 导出，在此过程中图片对应的 DOM 结构对用户不可见

## 开源依赖

根据 DOM 生成图片：[dom-to-image](https://github.com/tsayen/dom-to-image)

浏览器打包压缩文件：[js-zip](https://github.com/Stuk/jszip)

## 导出流程

![流程图](/images/zip流程.png)

## 问题记录

1. 生成过程中如何让组件不在页面中显示

```typescript
/** 隐藏组件显示样式 */
const COMP_HIDDEN_STYLE: React.CSSProperties = {
  position: "fixed",
  left: 0,
  top: 0,
  zIndex: -9999,
};
```

需要根据 DOM 加载组件，即 DOM 必须存在，dom-to-image 只支持 document 中可见元素的导出，使用**visibility：hidden**或**display: none**，均会生成空白图片。使用定位**left: -9999**，致使元素不可见，生成空白图片。zIndex 的方式可以做到隐藏元素，同时又不影响导出

2. zIndex 相对于父级元素，仍可能显示

```typescript
ReactDOM.createPortal(
  <div ref={domRef} style={{ ...COMP_HIDDEN_STYLE }}>
    {children}
  </div>,
  document.body
);
```

使用 createPortal 方法使元素脱离父组件 DOM，直接挂载在 body 上

3. 通过 ImageDownloader 的方式加载图片组件，ImageDownloader 多次渲染，触发多次 onImageAdd

```typescript
if (imageComponentListGetter.type === "sync")
  setPollingImageComponent(
    imageComponentListGetter?.imageComponentList?.map((item) => ({
      comp: item,
      key: uniqueId(),
    }))
  );
if (imageComponentListGetter.type === "async")
  setPollingImageComponent(
    (await imageComponentListGetter?.asyncGetImageComponentList())?.map(
      (item) => ({ comp: item, key: uniqueId() })
    )
  );
```

在 pollingImageComponent 数据更新前，为组件指定唯一 key，利用 key 值避免重复渲染

4. 进度展示

```typescript
const handleAddImageData = (fileData: string) => {
  imageDataArrRef.current.push(fileData);
  onProgress?.(imageDataArrRef.current.length / pollingImageComponent.length);

  if (imageDataArrRef.current.length === pollingImageComponent.length) {
    downloadZip();
  }
};
```

可以通过已生成的数据 length/需要导出的组件 length 显示

## 完整实现

导出组件 ImageZipExporter，记录图片生成状态，暴露接口给使用者消费

- 支持传入任何形式的导出按钮
- 支持以同步/异步方式获取需要导出的一组图片组件
- 支持进度显示

```typescript
import React, { useRef, useState } from "react";
import JSZip from "jszip";
import ImageDownloader from "./ImageDownloader";
import { uniqueId } from "lodash";

type IProps = {
  /** 图片组件列表获取 */
  imageComponentListGetter: {
    type: "sync" | "async";
    /** sync时，同步获取的数据 */
    imageComponentList?: React.ReactNode[];
    /** async时，异步获取数据的方法 */
    asyncGetImageComponentList?: () => Promise<React.ReactNode[]>;
  };
  /** 导出按钮 */
  exportButtonRender: React.ReactNode | string;
  /** 自定义图片名称，压缩包中命名为${customImageName}-${index} */
  imageName?: string;
  /** 自定义压缩包名称 */
  zipName?: string;
  /** 开始导出 */
  onStart?: () => void;
  /** 结束导出 */
  onFinish?: () => void;
  /** 导出失败 */
  onError?: () => void;
  /** 导出进度 1为100% */
  onProgress?: (progress: number) => void;
};

enum EXPORT_STATUS {
  /** 未开始 */
  NOT_START,
  /** 进行中 */
  EXPORTING,
}

/**
 * 图片压缩包导出组件
 * 由于图片导出需要DOM结构，需要保持组件一直在DOM中显示
 */
const ImageZipExporter = ({
  exportButtonRender,
  imageComponentListGetter,
  imageName = "image",
  zipName = "image",
  onStart,
  onFinish,
  onError,
  onProgress,
}: IProps) => {
  const imageDataArrRef = useRef<string[]>([]); // 图片二进制数据
  const [pollingImageComponent, setPollingImageComponent] = useState<
    { comp: React.ReactNode; key: string }[]
  >([]); // 需要下载的的图片组件
  const exportStatusRef = useRef<EXPORT_STATUS>(EXPORT_STATUS.NOT_START);

  const handleAddImageData = (fileData: string) => {
    imageDataArrRef.current.push(fileData);
    onProgress?.(imageDataArrRef.current.length / pollingImageComponent.length);

    if (imageDataArrRef.current.length === pollingImageComponent.length) {
      downloadZip();
    }
  };

  const reset = () => {
    imageDataArrRef.current = [];
    setPollingImageComponent([]);
    exportStatusRef.current = EXPORT_STATUS.NOT_START;
  };

  const downloadZip = () => {
    const zip = new JSZip();
    imageDataArrRef.current.forEach((imageData, index) => {
      zip.file(`${imageName}-${index}.png`, imageData, { binary: true });
    });
    zip
      .generateAsync({ type: "blob" })
      .then((content) => {
        const fileName = `${zipName}.zip`;
        const link = document.createElement("a");
        link.href = URL.createObjectURL(content);
        link.download = fileName;
        document.body.appendChild(link);
        link.click();
      })
      .then(() => {
        onFinish?.();
      })
      .catch((e) => {
        console.log("oops, generate zip failed", e);
      })
      .finally(() => {
        reset();
      });
  };

  const handleStart = async () => {
    if (exportStatusRef.current === EXPORT_STATUS.EXPORTING) return;

    // 设置需要下载的图片组件列表
    try {
      exportStatusRef.current = EXPORT_STATUS.EXPORTING;
      onStart?.();

      if (imageComponentListGetter.type === "sync")
        setPollingImageComponent(
          imageComponentListGetter?.imageComponentList?.map((item) => ({
            comp: item,
            key: uniqueId(),
          }))
        );

      if (imageComponentListGetter.type === "async")
        setPollingImageComponent(
          (await imageComponentListGetter?.asyncGetImageComponentList())?.map(
            (item) => ({ comp: item, key: uniqueId() })
          )
        );
    } catch (e) {
      console.log("oops, image components get failed");
      exportStatusRef.current = EXPORT_STATUS.NOT_START;
      onError?.();
    }
  };

  return (
    <React.Fragment>
      {<div onClick={handleStart}>{exportButtonRender}</div>}
      {pollingImageComponent?.map((item) => {
        return (
          <ImageDownloader key={item.key} onAddImageData={handleAddImageData}>
            {item.comp}
          </ImageDownloader>
        );
      })}
    </React.Fragment>
  );
};

export default ImageZipExporter;
```

图片下载组件 ImageDownloader，图片生成完毕后会使用 onAddImageData 通知 ImageZipExporter

```typescript
import React, { useRef, useEffect } from "react";
import domtoimage from "dom-to-image";
import ReactDOM from "react-dom";

type IProps = {
  /** 需要导出的图片组件 */
  children: React.ReactNode;
  /** 组件数据添加的回调函数 */
  onAddImageData: (imageData: string) => void;
};

/** 隐藏组件显示样式 */
const COMP_HIDDEN_STYLE: React.CSSProperties = {
  position: "fixed",
  left: 0,
  top: 0,
  zIndex: -9999,
};

const ImageDownloader = ({ children, onAddImageData }: IProps) => {
  const domRef = useRef(null);

  useEffect(() => {
    // 在组件加载后将 dom 元素导出为图片
    const node = domRef.current;
    domtoimage
      .toPng(node)
      .then((dataUrl: string) => {
        const imageData = atob(dataUrl.split(",")[1]);
        onAddImageData(imageData);
      })
      .catch((e: Error) => {
        console.log("oops, domtoimage failed", e);
      });
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  return ReactDOM.createPortal(
    <div ref={domRef} style={{ ...COMP_HIDDEN_STYLE }}>
      {children}
    </div>,
    document.body
  );
};

export default ImageDownloader;
```

---
title: "js图片格式"
date: 2021-09-13
draft: false
summary: "js图片格式"
---

# js图片格式

## base64转Blob

    function base64ToBlob(base64) {
        return new Promise(resolve => {
            if (typeof base64 !== "string") {
                return resolve(base64);
            }
            
            const bytes = window.atob(base64.split(",")[1]);
            // 处理异常，将ascii码小于0的转换为大于0
            const buffer = new ArrayBuffer(bytes.length);
            const arr = new Uint8Array(buffer);
            for (let i = 0; i < bytes.length; i++) {
                arr[i] = bytes.charCodeAt(i);
            }
            resolve(new Blob([buffer], { type: "image/jpeg" }));
        });
    }

## base64转File

    function dataURLtoFile(dataurl, filename) { 
        const arr = dataurl.split(",");
        const mime = arr[0].match(/:(.*?);/)[1];
        const bstr = atob(arr[1]);
        let n = bstr.length;
        const u8arr = new Uint8Array(n);
        while (n--) {
            u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr], filename, { type: mime });
    }

## base64转ArrayBuffer对象

    function base64ToArrayBuffer(base64) {
        base64 = base64.replace(/^data\:([^\;]+)\;base64,/gmi, "");
        const binary = atob(base64);
        const len = binary.length;
        const buffer = new ArrayBuffer(len);
        const view = new Uint8Array(buffer);
        for (let i = 0; i < len; i++) {
            view[i] = binary.charCodeAt(i);
        }
        return buffer;
    }

## 获取图片的exif的角度

    function getOrientation(arrayBuffer) {
        const dataView = new DataView(arrayBuffer);
        let length = dataView.byteLength;
        let orientation;
        let exifIDCode;
        let tiffOffset;
        let firstIFDOffset;
        let littleEndian;
        let endianness;
        let app1Start;
        let ifdStart;
        let offset;
        let i;
        // Only handle JPEG image (start by 0xFFD8)
        if (dataView.getUint8(0) === 0xFF && dataView.getUint8(1) === 0xD8) {
            offset = 2;
            while (offset < length) {
                if (dataView.getUint8(offset) === 0xFF && dataView.getUint8(offset + 1) === 0xE1) {
                    app1Start = offset;
                    break;
                }
                offset++;
            }
        }
        if (app1Start) {
            exifIDCode = app1Start + 4;
            tiffOffset = app1Start + 10;
            if (getStringFromCharCode(dataView, exifIDCode, 4) === "Exif") {
                endianness = dataView.getUint16(tiffOffset);
                littleEndian = endianness === 0x4949;

                if (littleEndian || endianness === 0x4D4D /* bigEndian */) {
                    if (dataView.getUint16(tiffOffset + 2, littleEndian) === 0x002A) {
                        firstIFDOffset = dataView.getUint32(tiffOffset + 4, littleEndian);

                        if (firstIFDOffset >= 0x00000008) {
                            ifdStart = tiffOffset + firstIFDOffset;
                        }
                    }
                }
            }
        }
        if (ifdStart) {
            length = dataView.getUint16(ifdStart, littleEndian);

            for (i = 0; i < length; i++) {
                offset = ifdStart + i * 12 + 2;
                if (dataView.getUint16(offset, littleEndian) === 0x0112 /* Orientation */) {

                    // 8 is the offset of the current tag's value
                    offset += 8;

                    // Get the original orientation value
                    orientation = dataView.getUint16(offset, littleEndian);

                    // Override the orientation with its default value for Safari (#120)
                    if (true) {
                        dataView.setUint16(offset, 1, littleEndian);
                    }
                    break;
                }
            }
        }
        return orientation;
    }

## ios图片旋转问题

    function rotateImg(img, direction, canvas) {
        // 最小与最大旋转方向，图片旋转4次后回到原方向
        // tslint:disable-next-line:variable-name
        const min_step = 0;
        // tslint:disable-next-line:variable-name
        const max_step = 3;
        if (img == null) {
            return;
        }
        const height = img.height;
        const width = img.width;
        let step = 2;
        if (step == null) {
            step = min_step;
        }
        if (direction === "right") {
            step++;
            // 旋转到原位置，即超过最大值
            // tslint:disable-next-line:no-unused-expression
            step > max_step && (step = min_step);
        } else {
            step--;
            // tslint:disable-next-line:no-unused-expression
            step < min_step && (step = max_step);
        }
        // 旋转角度以弧度值为参数
        const degree = step * 90 * Math.PI / 180;
        const ctx = canvas.getContext("2d");
        switch (step) {
            case 0:
                canvas.width = width;
                canvas.height = height;
                ctx.drawImage(img, 0, 0);
                break;
            case 1:
                canvas.width = height;
                canvas.height = width;
                ctx.rotate(degree);
                ctx.drawImage(img, 0, -height);
                break;
            case 2:
                canvas.width = width;
                canvas.height = height;
                ctx.rotate(degree);
                ctx.drawImage(img, -width, -height);
                break;
            case 3:
                canvas.width = height;
                canvas.height = width;
                ctx.rotate(degree);
                ctx.drawImage(img, -width, 0);
                break;
        }
    }

## 判断图片大小

    function testMaxSize(size, maxSize) {
        let isAllow = false;
        isAllow = size <= maxSize;
        if (!isAllow) {
            return false;
        }
        return isAllow;
    }

## 旋转图片实际例子

    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => {
        dealImage(reader.result, file, 800, (data) => {
            const size = this.testMaxSize(data.size, 1048576);
            if (!size) {
                Message.error("图片过大,请上传小于1M的图片");
                return;
            }
        })
    }

    function dealImage(base64Url, files, w, bacllback) {
        const newImage = new Image();
        let orientation;
        orientation = getOrientation(base64ToArrayBuffer(base64Url));
        newImage.src = base64Url;
        newImage.onload = async () => {
            const canvas = document.createElement("canvas");
            if (orientation && orientation !== 1) {
                if (orientation === 6) {
                    this.rotateImg(newImage, "left", canvas);
                    this.yasuo(canvas.toDataURL("image/jpeg", 0.8), w, callback, files);
                } else if (orientation === 8) {
                    this.rotateImg(newImage, "right", canvas);
                    this.yasuo(canvas.toDataURL("image/jpeg", 0.8), w, callback, files);
                } else if (orientation === 3) {
                    this.rotateImg(newImage, "right", canvas);
                    this.rotateImg(newImage, "right", canvas);
                    this.yasuo(canvas.toDataURL("image/jpeg", 0.8), w, callback, files);
                }
            } else {
                this.yasuo(base64Url, w, callback, files);
            }
        };
    }

    function yasuo(base64Url, w, callback, files) {
        let imgWidth;
        let imgHeight;
        let blob: any;
        const img = new Image();
        img.src = base64Url;
        img.onload = async () => {
            imgWidth = img.width;
            imgHeight = img.height;
            const canvas = document.createElement("canvas");
            const ctx: any = canvas.getContext("2d");
            if (Math.max(imgWidth, imgHeight) > w) {
                if (imgWidth > imgHeight) {
                    canvas.width = w;
                    canvas.height = w * imgHeight / imgWidth;
                } else {
                    canvas.height = w;
                    canvas.width = w * imgWidth / imgHeight;
                }
            } else {
                canvas.width = imgWidth;
                canvas.height = imgHeight;
            }
            ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
            blob = await this.dataURLtoFile(canvas.toDataURL("image/jpeg", 0.8), files.name);
            callback(blob);
        };
    }
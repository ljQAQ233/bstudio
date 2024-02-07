# Private init

`QemuVideoBochsModeSetup`->
```c++
Private->MaxMode = ModeData - Private->ModeData;
```

---


```c++
typedef struct {
  UINT64                                Signature;
  EFI_HANDLE                            Handle;
  EFI_PCI_IO_PROTOCOL                   *PciIo;
  UINT64                                OriginalPciAttributes;
  EFI_GRAPHICS_OUTPUT_PROTOCOL          GraphicsOutput;
  EFI_DEVICE_PATH_PROTOCOL              *GopDevicePath;
  //
  // The next two fields match the client-visible
  // EFI_GRAPHICS_OUTPUT_PROTOCOL_MODE.MaxMode field.
  //
  UINTN                                 MaxMode;
  QEMU_VIDEO_MODE_DATA                  *ModeData;
  EFI_GRAPHICS_OUTPUT_MODE_INFORMATION  *VmwareSvgaModeInfo;

  QEMU_VIDEO_VARIANT                    Variant;
  FRAME_BUFFER_CONFIGURE                *FrameBufferBltConfigure;
  UINTN                                 FrameBufferBltConfigureSize;
  UINT8                                 FrameBufferVramBarIndex;
  UINT16                                VmwareSvgaBasePort;
} QEMU_VIDEO_PRIVATE_DATA;
```

不准确的说, GraphicsOutput 的 成员相当于 public, 除它之外的所有成员都是 private

---

初始化接口 (Interface):
```c++
  GraphicsOutput->QueryMode = QemuVideoGraphicsOutputQueryMode;
  GraphicsOutput->SetMode   = QemuVideoGraphicsOutputSetMode;
  GraphicsOutput->Blt       = QemuVideoGraphicsOutputBlt;
```

---

之所以不太准确, 其一原因是 有一些 private 被暴露在外

```c++
  Private->GraphicsOutput.Mode->MaxMode = (UINT32) Private->MaxMode;
  Private->GraphicsOutput.Mode->Mode    = GRAPHICS_OUTPUT_INVALIDE_MODE_NUMBER;
```

[Src/Boot/Edk2/OvmfPkg/QemuVideoDxe/Driver.c]:
```c++
VOID
InitializeBochsGraphicsMode (
  QEMU_VIDEO_PRIVATE_DATA  *Private,
  QEMU_VIDEO_BOCHS_MODES  *ModeData
  );
```

Private 也不是完全隐藏, 只是不给专门的接口, 玩 C++ 的直接给个指针指着 Private 你也没啥办法是吧...

TODO: Code


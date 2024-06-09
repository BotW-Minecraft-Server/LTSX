# Visual Components

Visual Components（简称 VC），是 LTSX 其他模组使用的基础图形库。该图形库提供了如注解式 GUI 注册、快捷数据调用，改善的原版 GUI 组件等。

## 提供的内容

- 注解式 GUI 注册
- 更快捷的物品槽注册机制
- 快速的服务器 -> 客户端数据处理
- 内置 VLC 组件为 Windows 侧客户端提供视频播放功能
- 全息图像显示器
- 调色板功能，可以非常快速地为任意 GUI 组件附着颜色
- 内置 LTSX 基础图形组件，方便其他模组调用

### I. 颜色按钮组件

代码来自于：[world-comment/common/src/main/java/cn/zbx1425/worldcomment/gui/WidgetColorButton.java at master · zbx1425/world-comment (github.com)](https://github.com/zbx1425/world-comment/blob/master/common/src/main/java/cn/zbx1425/worldcomment/gui/WidgetColorButton.java)

注释：color 传参 16 进制颜色，如 0xFFC5E1A5

```java
public class WidgetColorButton extends Button {

    int color;

    public WidgetColorButton(int i, int j, int k, int l, Component component, int color, OnPress onPress) {
        super(i, j, k, l, component, onPress, Supplier::get);
        this.color = color;
    }

    @Override
    protected void renderWidget(GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        Minecraft minecraft = Minecraft.getInstance();
        if (this.active) {
            guiGraphics.setColor(((color >> 16) & 0xFF) / 255f, ((color >> 8) & 0xFF) / 255f,
                    (color & 0xFF) / 255f, this.alpha);
        } else {
            guiGraphics.setColor(1.0f, 1.0f, 1.0f, this.alpha);
        }
        RenderSystem.enableBlend();
        RenderSystem.enableDepthTest();
        guiGraphics.blitNineSliced(WIDGETS_LOCATION, this.getX(), this.getY(), this.getWidth(), this.getHeight(), 20, 4, 200, 20, 0, this.getTextureY());
        guiGraphics.setColor(1.0f, 1.0f, 1.0f, 1.0f);
        int i = this.active ? 0xFFFFFF : 0xA0A0A0;
        this.renderString(guiGraphics, minecraft.font, i | Mth.ceil(this.alpha * 255.0f) << 24);
    }

    private int getTextureY() {
        int i = 1;
        if (!this.active) {
            i = 0;
        } else if (this.isHoveredOrFocused()) {
            i = 2;
        }
        return 46 + i * 20;
    }
}

// how to use
private WidgetColorButton btnSendFeedback;
btnSendFeedback = new WidgetColorButton(
        SIDEBAR_OFFSET + CommentTypeButton.BTN_WIDTH * 3, baseY, CommentTypeButton.BTN_WIDTH * 2, SQ_SIZE,
        Component.translatable("gui.worldcomment.submit"), 0xFFC5E1A5,
        sender -> sendReport()
);
```

### II. 视频播放组件

代码：[NGoedix/WatchVideoMod (github.com)](https://github.com/NGoedix/WatchVideoMod/tree/1.18.2)

注释：该模组可以使用 `/playvideo <PLAYER> <URL>` 播放一个在线流媒体视频，按下 SHIFT+ESC 可以强制退出。

模组使用数据包推送到客户端，由服务器推送视频（SendVideoMessage）到客户端，由客户端执行具体的解码操作。

### III. 机械动力构思动画组件

使用 `/playcreatepotion <PLAYER> <POTION_NAME>` 给玩家推送一个机械动力构思界面


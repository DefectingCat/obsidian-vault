# CameraMoveScript

要想移动 Camera 首先需要创建一个继承自 Script3D 的类，通过重写父类的方法来实现对当前舞台中的相机进行移动、旋转等。

```tsx
export default class CameraMoveScript extends Laya.Script3D {}
```

Script 父类中的 `onMouseDown` 并没有多少作用，我们需要直接对舞台进行事件的监听。在 `onAwake` 时就监听其事件。

```tsx
export default class CameraMoveScript extends Laya.Script3D {
		/**
     * @inheritDoc
     */
    /*override*/ onAwake(): void {
        Laya.stage.on(Laya.Event.MOUSE_DOWN, this, this.mouseDown);
        Laya.stage.on(Laya.Event.MOUSE_UP, this, this.mouseUp);
        //Laya.stage.on(Event.RIGHT_MOUSE_OUT, this, mouseOut);
        this.camera = <Laya.Camera>this.owner;
    }
}

```

但并不监听其 `MOUSE_MOVE` 事件，我们只需要在 `MOUSE_DOWN` 时保存鼠标最后的位置，

```tsx
protected mouseDown(e: Event): void {
    this.camera.transform.localRotation.getYawPitchRoll(this.yawPitchRoll);

    this.lastMouseX = Laya.stage.mouseX;
    this.lastMouseY = Laya.stage.mouseY;
    this.isMouseDown = true;
}
```

并在 `onUpdate` 中根据偏移量更新相机的位置即可。

```tsx
/**
 * @inheritDoc
 */
/*override*/ onUpdate() {
    const elapsedTime = Laya.timer.delta;
    if (
        !isNaN(this.lastMouseX) &&
        !isNaN(this.lastMouseY) &&
        this.isMouseDown
    ) {
        const scene: Laya.Scene3D = this.owner.scene;
        // Laya.KeyBoardManager.hasKeyDown(87) &&
        //     this.moveForward(-this.speed * elapsedTime); //W
        // Laya.KeyBoardManager.hasKeyDown(83) &&
        //     this.moveForward(this.speed * elapsedTime); //S
        // Laya.KeyBoardManager.hasKeyDown(65) &&
        //     this.moveRight(-this.speed * elapsedTime); //A
        // Laya.KeyBoardManager.hasKeyDown(68) &&
        //     this.moveRight(this.speed * elapsedTime); //D
        // Laya.KeyBoardManager.hasKeyDown(81) &&
        //     this.moveVertical(this.speed * elapsedTime); //Q
        // Laya.KeyBoardManager.hasKeyDown(69) &&
        //     this.moveVertical(-this.speed * elapsedTime); //E

        const offsetX = Laya.stage.mouseX - this.lastMouseX;
        const offsetY = Laya.stage.mouseY - this.lastMouseY;

        const yprElem: Laya.Vector3 = this.yawPitchRoll;
        // yprElem.x -= offsetX * this.rotaionSpeed * elapsedTime;
        // yprElem.y -= offsetY * this.rotaionSpeed * elapsedTime;
        yprElem.x += offsetX * this.rotaionSpeed * elapsedTime;
        yprElem.y += offsetY * this.rotaionSpeed * elapsedTime;
        this._updateRotation();
    }
    this.lastMouseX = Laya.stage.mouseX;
    this.lastMouseY = Laya.stage.mouseY;
}
```

```tsx
/**
 * @private
 */
protected _updateRotation(): void {
    if (Math.abs(this.yawPitchRoll.y) < 1.5) {
        Laya.Quaternion.createFromYawPitchRoll(
            this.yawPitchRoll.x,
            this.yawPitchRoll.y,
            this.yawPitchRoll.z,
            this.tempRotationZ
        );
        this.tempRotationZ.cloneTo(this.camera.transform.localRotation);
        this.camera.transform.localRotation =
            this.camera.transform.localRotation;
    }
}
```
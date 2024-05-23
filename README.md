const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    physics: {
        default: 'arcade',
        arcade: {
            gravity: { y: 300 },
            debug: false
        }
    },
    scene: {
        preload: preload,
        create: create,
        update: update
    }
};

const game = new Phaser.Game(config);

let player;
let hooks;
let cursors;
let isSwinging = false;
let currentHook;

function preload() {
    this.load.image('background', 'path/to/background.png');
    this.load.image('player', 'path/to/player.png');
    this.load.image('hook', 'path/to/hook.png');
}

function create() {
    this.add.image(400, 300, 'background');

    player = this.physics.add.sprite(100, 450, 'player');
    player.setCollideWorldBounds(true);
    player.setBounce(0.2);
    player.setGravityY(300);

    hooks = this.physics.add.group({
        key: 'hook',
        repeat: 5,
        setXY: { x: 200, y: 100, stepX: 150 }
    });

    cursors = this.input.keyboard.createCursorKeys();

    this.physics.add.collider(player, hooks, hookPlayer, null, this);
}

function update() {
    if (cursors.left.isDown) {
        player.setVelocityX(-160);
    } else if (cursors.right.isDown) {
        player.setVelocityX(160);
    } else {
        player.setVelocityX(0);
    }

    if (cursors.up.isDown && player.body.touching.down) {
        player.setVelocityY(-330);
    }

    if (isSwinging) {
        if (cursors.left.isDown) {
            player.angle -= 5;
        } else if (cursors.right.isDown) {
            player.angle += 5;
        }

        player.x = currentHook.x + Math.cos(player.rotation) * 100;
        player.y = currentHook.y + Math.sin(player.rotation) * 100;

        if (cursors.down.isDown) {
            isSwinging = false;
            player.setVelocityY(-300);
            player.setAngularVelocity(0);
        }
    }
}

function hookPlayer(player, hook) {
    if (!isSwinging) {
        isSwinging = true;
        currentHook = hook;
        player.setVelocity(0);
        player.setGravityY(0);
    }
}

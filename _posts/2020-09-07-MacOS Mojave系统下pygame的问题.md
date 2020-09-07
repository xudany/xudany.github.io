---


title: MacOS Mojave系统下pygame的问题
date: 2020-09-07
categories:
- Pygame
tags:
- Pygame
- Python
- issue
---



跟着教程用Pygame做《外星人入侵》游戏这款游戏的时候，发现在MacOS Mojave生产环境下，运行Pygame相关程序的时候一直是黑色屏幕，不能正常显示画面，记录一下解决问题的过程。



### 产生问题的原因

https://github.com/pygame/pygame/issues/555



### 解决

网址：https://www.pygame.org/wiki/MacCompile

根据Pygame官网给出的解决步骤在MacOS Mojave环境进行特殊设置：

1. 安装 SDL依赖：

  ```bash
  $ brew install sdl2 sdl2_gfx sdl2_image sdl2_mixer sdl2_net sdl2_ttf
  ```

2. 安装XQuartz：

  ```bash
  $ brew install Caskroom/cask/xquartz
  ```

3. 安装Python 3.7.x:

  ```bash
  $ brew install python3
  ```

	Ps：如果之前已经有安装可以跳过这一步

4.  创建虚拟环境

  ```bash
  $ mkdir ~/tmp/; cd ~/tmp
  $ python3 -m venv .venv; source .venv/bin/activate
  ```

5. 安装Pygame
  最近12月21日的更新
  ```bash
  $ pip install git+https://github.com/pygame/pygame.git@89a6506
  ```

  最新的git repo
  ```bash
  $ pip install git+https://github.com/pygame/pygame.git
  ```

  Ps: 这个地方如果使用python3的话 需要将`pip`改为`pip3`, 下载速度慢的话可以改为国内的源

6. 验证Pygame自带的示例程序

  ```bash
  $ python -m pygame.tests
  ```

  如果是Python3，则是

  ```bash
  $ python3 -m pygame.tests
  ```



### 使用

在虚拟环境的目录下（~/tmp）执行想要运行的Pygame文件

```bash
$ python3 /Users/python-game/alien_invasion.py
```

Ps: 文件路径为绝对路径



### 仍存在的问题

只能在虚拟环境下执行Pygame文件，看后续需要，是否要解决此问题。






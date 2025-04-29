#!/bin/bash

# 主菜单函数
show_menu() {
    clear
    echo "======= Gensyn 节点管理工具 ======="
    echo "1. 安装基础环境（Python、Git、Homebrew）"
    echo "2. 查看 Gensyn 会话（screen）"
    echo "3. 安装并配置 ngrok"
    echo "4. 启动 ngrok 隧道（本地3000端口）"
    echo "0. 退出"
    echo "=================================="
    echo -n "请选择操作: "
}

# 安装基础环境
install_environment() {
    echo "正在安装 Homebrew、Python3、Git..."
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    brew install python3 git screen
    echo "基础环境安装完成。"
    read -p "按回车返回菜单..."
}

# 查看 screen 会话
view_sessions() {
    echo "当前 screen 会话："
    screen -ls
    read -p "按回车返回菜单..."
}

# 安装并配置 ngrok
install_ngrok() {
    if ! command -v ngrok &>/dev/null; then
        echo "正在下载安装 ngrok..."
        brew install --cask ngrok
    else
        echo "ngrok 已安装。"
    fi

    echo "请输入你的 ngrok authtoken（在 https://dashboard.ngrok.com 获取）:"
    read token
    ngrok config add-authtoken $token
    echo "ngrok 配置完成。"
    read -p "按回车返回菜单..."
}

# 启动 ngrok 隧道
start_ngrok() {
    echo "正在启动 ngrok 隧道映射 http://localhost:3000 ..."
    ngrok http 3000
    read -p "按回车返回菜单..."
}

# 主循环
while true; do
    show_menu
    read choice
    case $choice in
        1) install_environment ;;
        2) view_sessions ;;
        3) install_ngrok ;;
        4) start_ngrok ;;
        0) echo "退出程序"; exit 0 ;;
        *) echo "无效选项，请重新输入。"; sleep 1 ;;
    esac
done

import glob
import os
import configparser
import tkinter as tk
from tkinter import filedialog, messagebox



def load_keywords(filepath):
    """从文件中加载关键词"""
    with open(filepath, 'r', encoding='utf-8') as f:
        return [line.strip() for line in f.readlines()]


def convert_and_process_ass(keyword_filepath):
    keywords = load_keywords(keyword_filepath)
    """处理ASS文件：更改样式并删除敏感词"""
    # 定义头部关键词列表，这些关键词会用于识别文件的头部信息
    header_keywords = [
    "[Script Info]",
    "ScriptType:",
    "Collisions:",
    "PlayResX:",
    "PlayResY:",
    "Timer:",
    "WrapStyle:",
    "ScaledBorderAndShadow:",
    "[V4+ Styles]",
    "Format:",
    "Style:",
    "message_box",
    "[Events]"
    ]
    # 使用glob获取当前目录下所有的.ass文件
    ass_files = glob.glob('*.ass')
    print("关键词检测1 开始运行\n")
    # 遍历每一个.ass文件
    for ass_file in ass_files:
        print(f"正在处理文件: {ass_file}")  # 打印当前处理的文件名
        with open(ass_file, 'r', encoding='utf-8') as f:
            lines = f.readlines()
        
        new_lines = []
        for line in lines:
            # 检查头部信息
            if any(header_key in line for header_key in header_keywords):
                new_lines.append(line)
                continue
            
            # 检查敏感词
            remove_line = False
            for keyword in keywords:
                if ' ' in keyword:
                    # 多个关键词
                    all_found = all(word in line for word in keyword.split())
                    if all_found:
                        remove_line = True
                        break
                else:
                    # 单个关键词
                    if keyword in line:
                        remove_line = True
                        break

            if not remove_line:
                new_lines.append(line)

        # 写入新的行到文件
        with open(ass_file, 'w', encoding='utf-8') as f:
            f.writelines(new_lines)
        print(f"完成文件: {ass_file}")  # 完成处理后打印
    print("\n关键词检测1 完成运行\n")



def convert_and_process_no_ass(no_keyword_filepath):
    """处理文件：更改样式并根据特定规则删除敏感词"""
    # 定义头部关键词列表
    header_keywords = [
        "[Script Info]",
        "ScriptType:",
        "Collisions:",
        "PlayResX:",
        "PlayResY:",
        "Timer:",
        "WrapStyle:",
        "ScaledBorderAndShadow:",
        "[V4+ Styles]",
        "Format:",
        "Style:",
        "message_box",
        "[Events]"
    ]

    # 加载关键词
    keywords = load_keywords(no_keyword_filepath)

    # 使用glob获取当前目录下所有的.ass文件
    ass_files = glob.glob('*.ass')
    print("——————————————————\n关键词检测2 开始运行\n")
    # 遍历每一个.ass文件
    for ass_file in ass_files:
        print(f"正在处理文件: {ass_file}")  # 打印当前处理的文件名

        new_lines = []
        with open(ass_file, 'r', encoding='utf-8', errors='ignore') as f:
            for line in f:
                if any(header_key in line for header_key in header_keywords):
                    new_lines.append(line)
                    continue

                # 检查敏感词
                remove_line = any(keyword in line for keyword in keywords)

                if not remove_line:
                    new_lines.append(line)

        # 写入新的行到文件
        with open(ass_file, 'w', encoding='utf-8') as f:
            f.writelines(new_lines)
        print(f"完成文件: {ass_file}")  # 完成处理后打印
    print("\n关键词检测2 完成运行\n")


def save_last_file_path(keyword_path, no_keyword_path, config_file='settings.ini'):
    """保存上次选择的文件路径到配置文件中"""
    config = configparser.ConfigParser()
    config['Files'] = {
        'keyword_file': keyword_path,
        'no_keyword_file': no_keyword_path
    }
    with open(config_file, 'w') as configfile:
        config.write(configfile)

def get_last_file_paths(config_file='settings.ini'):
    """从配置文件中获取上次选择的文件路径"""
    config = configparser.ConfigParser()
    config.read(config_file)
    if 'Files' in config:
        keyword_file = config['Files'].get('keyword_file', '')
        no_keyword_file = config['Files'].get('no_keyword_file', '')
        return keyword_file, no_keyword_file
    return '', ''


def choose_file(entry, is_keyword=True):
    """选择文件并更新文本框内容，同时保存最新的文件路径到配置文件。"""
    # 获取当前的文件路径配置
    current_keyword_path, current_no_keyword_path = get_last_file_paths()
    # 根据当前操作的是关键词文件还是无关键词文件，设置初始目录
    initialdir = os.path.dirname(current_keyword_path) if is_keyword else os.path.dirname(current_no_keyword_path)
    filepath = filedialog.askopenfilename(initialdir=initialdir, filetypes=(("Text files", "*.txt*"), ("All files", "*.*")))
    
    if filepath:
        entry.delete(0, tk.END)  # 清空文本框
        entry.insert(0, filepath)  # 插入新的文件路径
        
        # 根据是关键词文件还是无关键词文件来更新路径
        if is_keyword:
            save_last_file_path(filepath, current_no_keyword_path)
        else:
            save_last_file_path(current_keyword_path, filepath)



def start_processing(keyword_file_entry, no_keyword_file_entry):
    """根据选择的文件执行相应的处理函数"""
    keyword_filepath = keyword_file_entry.get()
    no_keyword_filepath = no_keyword_file_entry.get()

    if keyword_filepath:
        print(f"处理关键词文件: {keyword_filepath}")
        convert_and_process_ass(keyword_filepath)  # 实际使用时取消注释
    else:
        messagebox.showinfo("提示", "没有选择关键词文件")

    if no_keyword_filepath:
        print(f"处理无关键词文件: {no_keyword_filepath}")
        convert_and_process_no_ass(no_keyword_filepath)  # 实际使用时取消注释
    else:
        messagebox.showinfo("提示", "没有选择无关键词文件")

def main():
    root = tk.Tk()
    root.title("文件处理工具")

    # 尝试获取上次文件路径
    keyword_file_path, no_keyword_file_path = get_last_file_paths()

    # 关键词文件选择
    keyword_frame = tk.Frame(root)
    keyword_frame.pack(padx=10, pady=5)
    keyword_entry = tk.Entry(keyword_frame, width=50)
    keyword_entry.insert(0, keyword_file_path)  # 填充上次路径
    keyword_entry.pack(side=tk.LEFT)
    btn_keyword_browse = tk.Button(keyword_frame, text="浏览", command=lambda: choose_file(keyword_entry, is_keyword=True))
    btn_keyword_browse.pack(side=tk.LEFT, padx=5)

    # 无关键词文件选择
    no_keyword_frame = tk.Frame(root)
    no_keyword_frame.pack(padx=10, pady=5)
    no_keyword_entry = tk.Entry(no_keyword_frame, width=50)
    no_keyword_entry.insert(0, no_keyword_file_path)  # 填充上次路径
    no_keyword_entry.pack(side=tk.LEFT)
    btn_no_keyword_browse = tk.Button(no_keyword_frame, text="浏览", command=lambda: choose_file(no_keyword_entry, is_keyword=False))
    btn_no_keyword_browse.pack(side=tk.LEFT, padx=5)

    # 开始处理按钮
    btn_start = tk.Button(root, text="开始处理", command=lambda: start_processing(keyword_entry.get(), no_keyword_entry.get()))
    btn_start.pack(pady=5)

    # 更新保存文件路径的逻辑
    def start_processing(keyword_filepath, no_keyword_filepath):
        """根据选择的文件执行相应的处理函数，并保存文件路径"""
        if keyword_filepath:
            print(f"处理关键词文件: {keyword_filepath}")
            convert_and_process_ass(keyword_filepath)  # 根据需要调用
        if no_keyword_filepath:
            print(f"处理无关键词文件: {no_keyword_filepath}")
            convert_and_process_no_ass(no_keyword_filepath)  # 根据需要调用
        # 保存文件路径
        save_last_file_path(keyword_filepath, no_keyword_filepath)

    root.mainloop()

if __name__ == "__main__":
    main()

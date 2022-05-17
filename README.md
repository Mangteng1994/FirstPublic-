# 清理siyuan未引用资源
import os
import datetime
import sqlite3
from shutil import copyfile, move

with open("siyuan_cache.txt", "r", encoding="utf-8") as f:
    siyuan_data = f.readline()
siyuan_db = f"{siyuan_data}/temp/siyuan.db"
os.remove("siyuan.db")
copyfile(siyuan_db, "siyuan.db")

assets_folder = f"{siyuan_data}/data/assets"

# get siyuan used file list
con = sqlite3.connect("siyuan.db")
cur = con.cursor()
siyuan_used_file = [r[6] for r in cur.execute("SELECT * FROM assets")]
con.close()

# get assets all files
all_assets_file = os.listdir(assets_folder)

rm_list = [k for k in all_assets_file if k not in siyuan_used_file]

if ".siyuan" in rm_list:
    rm_list.remove(".siyuan")

for k in rm_list:
    print(k)

if len(rm_list) == 0:
    print("尚未找到未使用的资源文件")
else:
    ans = input("找到以上未使用的资源，是否删除？[y/n]")
    if ans in ["y", "yes"]:
        # get current time and
        del_time = datetime.datetime.now().strftime('%Y-%m-%d-%H%M%S')
        trash_folder = f"{assets_folder}/.siyuan/history/{del_time}-delete/assets"
        os.makedirs(trash_folder)
        for d in rm_list:
            move(f"{assets_folder}/{d}", f"{trash_folder}/{d}")
        print(f"已将上述文件移动到{trash_folder}回收站内")

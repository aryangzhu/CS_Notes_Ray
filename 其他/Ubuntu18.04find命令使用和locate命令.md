1. 列出当前目录及子目录下所有文件和文件夹
	find.
2. 在/home目录下查找以.txt结尾的文件名
	find /home -name"*.txt"
3. 在全局范围内查找string.hpp文件
	sudo find /-name "string.hpp"
4. locate -文件名 个人感觉比较容易上手，但是过于消耗性能
5. pwd 显示文件全路径
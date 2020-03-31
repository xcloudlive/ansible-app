# ansible-app
### 说明
下面以一个ansible脚本为例，说明维护中的通用模板：
```
---
- hosts: all
  vars:
    check_port:
      - 8080
      - 80
    result: ok
  tasks:
    - name: Check if port is listening
      block:
        - name: do checking....
          wait_for:
            port: "{{ item }}"
            timeout: 5
            msg: "Timeout waiting for {{ item }} to respond"
          loop: "{{ check_port }}"
      rescue:
        - set_fact:
            result: failed
        - fail: 
            msg: error is happened
          when: result == "failed"
        - name:  
          debug:
            msg: 'I also never execute :-('
      always:
        - name: always to check result
          debug:
            msg: "all tasks are finished, result is {{ result }}"  
```
### vars
变量循环定义，result默认为ok，用来标识整个任务的最终运行状态。相关的变量也要在此进行定义。
### block
该处是任务的循环体，参考：https://docs.ansible.com/ansible/latest/user_guide/playbooks_blocks.html
### rescue
任务循环中异常处理
```
        - set_fact:
            result: failed
```
设置result为failed
```
        - fail: 
            msg: error is happened
          when: result == "failed"  
```
抛出异常
```
        - name:  
          debug:
            msg: 'I also never execute :-('
```            
该语句正常情况下，不会执行
### always
任务最终执行结果，正常打印：all tasks are finished, result is ok；错误的时候打印：all tasks are finished, result is failed

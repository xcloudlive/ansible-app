### vars
变量名：check_port，以列表形式列出需要检查的端口
### tasks
```
    - name: Gathering service facts
      service_facts:
      register: services_state
```

> 通过获得目标服务器的service fact，并赋值给services_state

```
    - name: print all of service status
      debug: var=services_state.ansible_facts.services
```

> 打印service fact里面包含的服务信息

```
        - assert: 
            that:
              - item.name in services
              - services[item.name].state == item.status
            fail_msg: "{{ item.name }} is not in status {{ item.status }}" 
            msg: "{{ item.name }} is in status {{ item.status }}" 
          vars:
            services: "{{ services_state.ansible_facts.services }}"
          # ignore_errors: true
          with_items: "{{ service_list }}"
```
> 针对service_list变量进行循环，通过assert模块进行判断（https://docs.ansible.com/ansible/latest/modules/assert_module.html）

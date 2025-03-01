install-nvidia-cuda
=========

Install pre-compiled or dkms Nvidia drivers and cuda-toolkit on:  
- RHEL8  
- RHEL9  
- Ubuntu 22.04  
- Ubuntu 24.04  
- Debian 12  

Other distros in these families may also work.  
  
Based on the instructions from:  
- https://docs.nvidia.com/datacenter/tesla/driver-installation-guide/  
- https://docs.nvidia.com/cuda/cuda-installation-guide-linux/  

Requirements
------------

See Nvidia docs for system requirements.  
Drivers and tool-kit can take up 10Gb+ on some systems.  

Role Variables
--------------

- `driver_version` - Default `propriety`.  
  `open` for the latest open drivers.  
  `propriety` for the latest propriety drivers.  
   Or another value from https://docs.nvidia.com/datacenter/tesla/driver-installation-guide/index.html#precompiled-streams.  
   Open drivers require a 'Turing' (RTX 20xx, 16xx, etc.) or newer Nvidia GPU.  
- `allow_autoremove` - Default `false`. If autoremove should run when packages are installed or removed.  
- `driver_packages` - Default `true`. Install (or remove) Nvidia driver packages.  
- `cuda_packages` - Default `true`. Install (or remove) the CUDA toolkit packages.  

Dependencies
------------

- `community.general.dnf_config_manager` module  

Example Playbook
----------------

Can test the ouput of `lspci` to only run on nodes with Nvidia GPU hardware.
The propriety, pre-compiled drivers should work with older, pre-Turing, GPUs if required.  
```
---
- name: Install CUDA on nodes with GPUs
  hosts: all
  become: true
  gather_facts: true
  pre_tasks:
  - name: Check which nodes have NVIDIA GPUs
    register: lspci_result
    changed_when: false
    check_mode: false
    ansible.builtin.command:
      cmd: "lspci"

  tasks:
  - name: Install CUDA
    vars:
      driver_version: propriety
    when: lspci_result['stdout'] | regex_search('vga.*nvidia', ignorecase=true, multiline=false)
    ansible.builtin.include_role:
      name: install_nvidia_cuda
```

License
-------

BSD-3-Clause  


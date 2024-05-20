# Manual, ad-hoc BTF generation process

## Ubuntu

* If VM is running in GKE/GCP
    * `gcloud compute ssh node_or_instance_name`
    * `sudo -i`
* Follow instructions for getting `-dbgsym.ddeb` packages in https://wiki.ubuntu.com/Debug%20Symbol%20Packages
* `apt-get install linux-image-$(uname -r)-dbgsym dwarves llvm`
* `cd /usr/lib/debug/boot`
* `pahole --btf_encode vmlinux-$(uname -r)`
* `llvm-objcopy --only-section .BTF vmlinux-$(uname -r) ~unprivileged_user_name/$(uname -r).btf`
* Verify the btf file with `objdump -h 5.4.0-1055-aws-extra.btf` and make sure it has a `.BTF` section
* `xz ~unprivileged_user_name/$(uname -r).btf`
* chown unprivileged_user_name ~unprivileged_user_name/$(uname -r).btf.xz
* If VM is running in GKE/GCP
    * `gcloud compute scp node_or_instance_name:kernel_rev_string.btf.xz .`

## Amazon Linux 2

Run the [`tools/btf_gen/amazon-linux-2.sh`](../tools/btf_gen/amazon-linux-2.sh) script. For example,

```sh
../tools/btf_gen/amazon-linux-2.sh 4.14.336-256.559.amzn2.x86_64
```

Alternatively, you may also follow the instructions below to generate the file manually.

- Run an interactive shell in a container created using a relevant Amazon Linux 2 image, e.g. `docker run -it amazonlinux:2.0.20190823.1 bash`
- Enable the yum debuginfo repository by editing `/etc/yum.repos.d/amzn2-core.repo`
- Install the `debuginfo` package for your kernel. For example, if you installed `kernel-4.14.138-114.102.amzn2.x86_64`:
  ```
  sudo yum install kernel-debuginfo-4.14.138-114.102.amzn2.x86_64
  ```
- `sudo yum install dwarves llvm`
- `sudo cp /usr/lib/debug/usr/lib/modules/$(uname -r)/vmlinux ~/`
- `cd ~`
- `pahole --btf_encode vmlinux`
- `llvm-objcopy --only-section .BTF vmlinux $(uname -r).btf`
- Verify the btf file with `objdump -h $(uname -r).btf` and make sure it has a `.BTF` section
- `xz $(uname -r).btf`
- Copy the resulting `$(uname -r).btf.xz` file to the machine where you want to use it.

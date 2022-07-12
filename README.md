# ec2
AWS CLI Wrapper for EC2 management.

## Installation

Use homebrew:

    $ brew install rcmdnk/rcmdnkpac/ec2

or put `bin/ec2` in anywhere in the PATH.

## Usage

    $ ec2 [-i <instance_id>] [-f <name_filter>] [-g <gpu_filter>] [-p <cpu_filter>] [-T <template>] [-t <instance_type>] [-k <ssh_key>] [-u <ssh_user>] [-r <running_only>] [-s <selection_tool>] [-h] <subcommand>

Subcommands:

* help: Show help.
* launch: Launch a new instance.
* list: List up instances.
* ls: Alias of list
* mosh: Connect to an instance with mosh.
* ssh: Connect to an instance with mosh.
* start: Start instances.
* stop: Stop instances.
* template: Get the launch template from an instance.
* terminate: Terminate instances.
* types: Show instance types.

## AWS CLI Profile

If you want to use a profile other than default,
set:

    export AWS_PROFILE=xxx

before using ec2.

## Configuration

Use **~/.config/ec2/config**.

Parameters can be set like:

    name_filter=my-instance

`#` can be used to comment out the line.

Available parameters are:

    $ ec2 [-i <instance_id>] [-f <name_filter>] [-g <gpu_filter>] [-p <cpu_filter>] [-T <template>] [-t <instance_type>] [-k <ssh_key>] [-u <ssh_user>] [-r <running_only>] [-s <selection_tool>] [-h] <subcommand>

* instance_id: Assign instance id to be managed.
* name_filter: Only instances which includes this value is listed.
* gpu_filter: Filter to pick up instance type by gpu.
* cpu_filter: Filter to pick up instance type by cpu.
* template: Launch template for launch command.
* instance_type: Instance type for launch command.
    * If `select` is passed, you can choose the type from the list.
* ssh_key: Key for ssh.
* ssh_user: User for ssh.
* running_only: Show only running instances at ssh/mosh
* selection_tool: Selection tool list, separated by `,`. Default value is `sentaku,peco,fzy,fzf`.
    * The first one found is used. If nothing, bash's `select` is used.
    * Tools ref:
        * [sentaku](https://github.com/rcmdnk/sentaku/)
        * [peco](https://github.com/peco/peco)
        * [fzy](https://github.com/jhawthorn/fzy)
        * [fzf](https://github.com/junegunn/fzf)

These parameters can be set by arguments, too.

## Examples

### Manage my instances with prefix "my-insatances"

Make ~/.config/ec2/config file as follows:

    name_filter=my-instances
    ssh_key=~/.ssh/my_ssh.pem
    ssh_user=ec2-user

This will show only instances which include `my-instances` in the Name.

It uses the key **~/.ssh/my_ssh.pem** at `ec2 ssh` or `ec2 mosh`, with the user name `ec2-user`.

### Make a template and launch with it

Launch an EC2 instance as you like at AWS.

Then, do:

    $ mkdir -p ~/.config/ec2/
    $ ec2 template > ~/.config/ec2/my_template.json

Select your instance for the template.

It is better to make templates for each `AvailabilityZone` (`SubnetId`).

If necessary, edit generated json files (`VolumeSize`, `ImageId`, `KeyName`, etc...).

`InstanceType` is set as the default, and it can be changed as described below.


To launch a new instance, do:

    $ ec2 -T ~/.config/ec2/my_template.json launch

This will launch a new instance with the same environments as the selected instance.

If you want to change the instance type, give `-t <instance_type>` option:

    $ ec2 -t r3.large -T ~/.config/ec2/my_template.json launch

If you give `-t select`, you can choose the instance type from the list.

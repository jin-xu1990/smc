# Use the SMC client in one-time job mode {#task_827230 .task}

In addition to the Daemon mode, you can also run the SMC client in one-time job mode to complete a migration. All operations required for the migration are completed on the migration source. This topic describes how to use the client to migrate a source server in one-time job mode without performing operations in the SMC console. Migration sources refer to IDC servers, virtual machines, cloud hosts on other platforms, or other types of servers that you want to migrate. Migration sources are also known as source servers.

You must complete preparations before performing operations on migration sources. For more information, see [Preparations \(before you start\)](../reseller.en-US/User Guide/Preparations (before you start).md#).

The basic process to use the SMC client in one-time job mode is as follows:

1.  [Download and decompress the SMC client package](#step_n8y_y2p_4li)
2.  [Configure the migration source and destination](#step_8sr_1nx_vy9)
3.  [\(Optional\) Exclude files or directories from migration.](#step_oa6_e16_n99)
4.  [Run the SMC client in one-time job mode](#step_as4_pin_hb6)

1.  Download and decompress the SMC client package. 
    1.  On the migration source, download the [SMC client](https://p2v-tools.oss-cn-hangzhou.aliyuncs.com/smc/Alibaba_Cloud_Migration_Tool.zip).
    2.  Decompress the SMC client package. The SMC client is available for Windows and Linux in both the 32-bit version \(i386\) and the 64-bit version \(x86\_64\). Select the version compatible with the migration source. The following figure shows the decompressed client folder.

        ![smc_client_1](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/630372/156630050450475_en-US.png)

    3.  Decompress the selected client version. The following figure shows the files and directories stored in the decompressed folder.

        ![smc-client-2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/630335/156630050449979_en-US.png)

        |File or folder|Description|
        |:-------------|:----------|
        |go2aliyun\_client.exe|The Windows CLI executable file.|
        |go2aliyun\_gui.exe|The Windows GUI executable file. For more information about the GUI version, see [Use the Windows GUI version of the SMC client](../reseller.en-US/Best Practices/Use the Windows GUI version of the SMC client.md#).|
        |go2aliyun\_client|The Linux CLI executable file.|
        |user\_config.json|The configuration file of the migration source and destination.|
        |Excludes|The folder in which to add directories to exclude from migration.|
        |client\_data|The migration data file, which includes the intermediate instance information and migration progress.|

2.  Configure the migration source and destination. 
    1.  Open the user\_config.json file. The user\_config.json file in its initial state is as follows:

        ``` {#codeblock_klu_ukb_nby}
        {
            "access_id": "",
            "secret_key": "",
            "region_id": "",
            "image_name": "",
            "system_disk_size": -1,
            "platform": "",
            "data_disks": [],
            "bandwidth_limit": 0
        }
        ```

    2.  Configure the migration source and destination based on the [migration parameters](#table_wej_b1q_027) and [data disk parameters](#table_b52_fiy_2vv). 

        |Parameter|Type|Required|Description|
        |:--------|:---|:-------|:----------|
        |access\_id|String|Yes|The AccessKey ID of your Alibaba Cloud account or RAM user account. For more information, see [Create an AccessKey pair](../../../../../reseller.en-US/General Reference/Create an AccessKey.md#).|
        |secret\_key|String|Yes|The AccessKey Secret of your Alibaba Cloud account or RAM user account. For more information, see [Create an AccessKey pair](../../../../../reseller.en-US/General Reference/Create an AccessKey.md#).|
        |region\_id|String|Yes|The ID of the Alibaba Cloud region to migrate your migration source to. For more region ID values, see [Regions and zones](../../../../../reseller.en-US/General Reference/Regions and zones.md#).|
        |image\_name|String|Yes|The name of the target Alibaba Cloud image generated by SMC for your migration source. Naming conventions:         -   The name cannot be the same as that of an existing image in the same region.
        -   The name must be 2 to 128 characters in length and can contain letters, digits, colons \(:\), underscores \(\_\), and hyphens \(-\). It must start with a letter and cannot start with http:// or https://.
 |
        |system\_disk\_size|Integer|Yes|The system disk size of the target Alibaba Cloud ECS instance. Unit: GiB. Valid values: 20 to 500. **Note:** The parameter value must be greater than the actual used space of the system disk on the source server. For example, if the size of the source disk was 500 GiB but the actual used space was only 100 GiB, you would only need to set this parameter to a value greater than 100 GiB.

 |
        |platform|String|No|The operating system of the migration source. Valid values: Windows Server 2003, Windows Server 2008, Windows Server 2012, Windows Server 2016, CentOS, Ubuntu, SUSE, OpenSUSE, Debian, RedHat, and Others Linux. **Note:** The value of the `platform` parameter must be case-sensitive.

 |
        |bandwidth\_limit|Integer|No|The maximum bandwidth during migration. Unit: Kbit/s. The default value is 0, which indicates unlimited bandwidth.

 |
        |data\_disks|Array|No|The list of data disks attached to the target Alibaba Cloud ECS instance. An ECS instance can have a maximum of 16 attached data disks. For more information about the data disk parameters, see [Data disk parameters](#table_b52_fiy_2vv).|

        |Parameter|Type|Required|Description|
        |:--------|:---|:-------|:----------|
        |data\_disk\_index|Integer|Yes|The index number of a data disk attached to the target Alibaba Cloud ECS instance. Valid values: 1 to 16 Default value: 1

 |
        |data\_disk\_size|Integer|Yes|The size of a data disk attached to the target Alibaba Cloud ECS instance. Unit: GiB. Valid values: 20 to 32768. **Note:** The parameter value must be greater than the actual used space of the data disk on the source server. For example, if the size of the source disk was 500 GiB but the actual used space was only 100 GiB, you would only need to set this parameter to a value greater than 100 GiB.

 |
        |src\_path|String|Yes|The source directory of a data disk. Examples:         -   In Windows, specify a drive letter, such as `D:`, `E:`, or `F:`.
        -   In Linux, specify a directory, such as /mnt/disk1, mnt/disk2, or /mnt/disk3.

**Note:** It cannot be the root directory or system directories such as /bin, /boot, /dev, /etc, /lib, /lib64, /sbin, /usr, and /var.

 |

        -   Scenario 1: Migrate a Windows server without data disks to the China \(Hangzhou\) region

            -   The source server is configured as follows:
                -   Operating system: Windows Server 2008
                -   System disk size: 30 GiB
            -   Migration destination:
                -   Target region: China \(Hangzhou\) \(`cn-hangzhou`\)
                -   Image name: CLIENT\_IMAGE\_WIN08\_01
                -   System disk size: 50 GiB
            ``` {#codeblock_l2p_mew_sve}
            {
                "access_id": "YourAccessKeyID",
                "secret_key": "YourAccessKeySecret",
                "region_id": "cn-hangzhou",
                "image_name": "CLIENT_IMAGE_WIN08_01",
                "system_disk_size": 50,
                "platform": "Windows Server 2008",
                "data_disks": [],
                "bandwidth_limit": 0
            }
            ```

        -   Scenario 2: Migrate a Windows server with data disks to the China \(Hangzhou\) region

            In this scenario, the Windows server from Scenario 1 has two data disks attached. The drive letters and sizes of the data disks are as follows:

            -   Sizes of source data disks:
                -   D: 50 GiB
                -   E: 100 GiB
            -   Sizes of target data disks:
                -   D: 100 GiB
                -   E: 150 GiB
            ``` {#codeblock_srh_1ou_ymx}
            {
                "access_id": "YourAccessKeyID",
                "secret_key": "YourAccessKeySecret",
                "region_id": "cn-hangzhou",
                "image_name": "CLIENT_IMAGE_WIN08_01",
                "system_disk_size": 50,
                "platform": "Windows Server 2008",
                "data_disks":  [ {
                        "data_disk_index": 1,
                        "data_disk_size": 100,
                        "src_path": "D:"
                    }, {
                        "data_disk_index": 2,
                        "data_disk_size": 150,
                        "src_path": "E:"
                    }
                ],
                "bandwidth_limit": 0
            }
            ```

        -   Scenario 3: Migrate a Linux server without data disks to the China \(Hangzhou\) region

            -   The source server is configured as follows:
                -   Operating system: CentOS 7.2
                -   System disk size: 30 GiB
            -   Migration destination:
                -   Target region: China \(Hangzhou\) \(`cn-hangzhou`\)
                -   Image name: CLIENT\_IMAGE\_CENTOS72\_01
                -   System disk size: 50 GiB
            ``` {#codeblock_ha5_o4o_zub}
            {
                "access_id": "YourAccessKeyID",
                "secret_key": "YourAccessKeySecret",
                "region_id": "cn-hangzhou",
                "image_name": "CLIENT_IMAGE_CENTOS72_01",
                "system_disk_size": 50,
                "platform": "CentOS",
                "data_disks": [],
                "bandwidth_limit": 0
            }
            ```

        -   Scenario 4: Migrate a Linux server with data disks to the China \(Hangzhou\) region

            In this scenario, the Linux server from Scenario 3 has two data disks attached. The drive letters and sizes of the data disks are as follows:

            -   Sizes of source data disks:
                -   /mnt/disk1: 50 GiB
                -   /mnt/disk2: 100 GiB
            -   Sizes of target data disks:
                -   /mnt/disk1: 100 GiB
                -   /mnt/disk2: 150 GiB
            ``` {#codeblock_a55_jh4_i94}
            {
                "access_id": "YourAccessKeyID",
                "secret_key": "YourAccessKeySecret",
                "region_id": "cn-hangzhou",
                "image_name": "CLIENT_IMAGE_CENTOS72_01",
                "system_disk_size": 50,
                "platform": "CentOS",
                "data_disks":  [ {
                        "data_disk_index": 1,
                        "data_disk_size": 100,
                        "src_path": "/mnt/disk1"
                    }, {
                        "data_disk_index": 2,
                        "data_disk_size": 150,
                        "src_path": "/mnt/disk2"
                    }
                ],
                "bandwidth_limit": 0
            }
            ```

3.  \(Optional\) Exclude files or directories from migration. The configuration files are located in the Excludes directory of the SMC client, including:

    -   A system disk configuration file: rsync\_excludes\_win.txt \(for Windows servers\) or rsync\_excludes\_linux.txt \(for Linux servers\)

    -   A data disk configuration file: named by adding a suffix disk \[disk index number\] to the system disk, such as rsync\_excludes\_win\_disk1.txt \(for Windows servers\) or rsync\_excludes\_linux\_disk1.txt \(for Linux servers\).

    **Note:** If a configuration file is lost or deleted by accident, you can create another one.

    -   Example 1: Exclude files or directories from migration of a Windows Server
        -   System disk:
            -   Specify the files or directories to be excluded:

                ``` {#codeblock_3t6_0ms_qg4}
                C:\MyDirs\Docs\Words
                C:\MyDirs\Docs\Excels\Report1.txt
                ```

            -   Add the following information to the rsync\_excludes\_win.txt file:

                ``` {#codeblock_okd_vsu_4tr}
                /MyDirs/Docs/Words/
                /MyDirs/Docs/Excels/Report1.txt
                ```

        -   Data disk:

            -   Specify the files or directories to be excluded:

                ``` {#codeblock_bob_ob5_vwj}
                D:\MyDirs2\Docs2\Words2
                D:\MyDirs2\Docs2\Excels\Report2.txt
                ```

            -   Add the following information to the rsync\_excludes\_win\_disk1.txt file:

                ``` {#codeblock_qe6_bot_smu}
                /MyDirs2/Docs2/Words2/
                /MyDirs2/Docs2/Excels2/Report2.txt
                ```

            **Note:** 

            To exclude a Windows directory, you must perform the following operations:

            -   Remove the prefix of the directory \(scr\_path\). In the preceding example, you must remove `D:`.
            -   Replace `\` with `/`.
    -   Example 2: Exclude files or directories from migration of a Linux server
        -   System disk \(root directory/\):

-   Specify the files or directories to be excluded:

    ``` {#codeblock_tb7_2z3_1nq}
    /var/mydirs/docs/words
    /var/mydirs/docs/excels/report1.txt
    ```

-   Add the following information to the rsync\_excludes\_linux.txt file:

    ``` {#codeblock_zce_114_yii}
    /var/mydirs/docs/words/
    /var/mydirs/docs/excels/report1.txt
    ```

        -   Data disk:

-   Specify the files or directories to be excluded:

    ``` {#codeblock_utm_f1l_t3o}
    /mnt/disk1/mydirs2/docs2/words2
    /mnt/disk1/mydirs2/docs2/excels2/report2.txt
    ```

-   Add the following information to the rsync\_excludes\_linux\_disk1.txt file:

    ``` {#codeblock_8uy_yq7_dib}
    /mydirs2/docs2/words2/
    /mydirs2/docs2/excels2/report2.txt
    ```

            **Note:** To exclude a Linux directory, you must remove the prefix of the directory \(scr\_path\). In the preceding example, you must remove /mnt/disk1.

4.  Run the SMC client in one-time job mode. 

    -   For Windows servers, select one of the following methods to run the SMC client:
        -   Windows GUI version

            1.  Double-click to run go2aliyun\_gui.exe.
            2.  Choose **Config** \> **One-Time Job Mode**.
            3.  Click **OK**.
            4.  Click **Start**.
            **Note:** When you run the program, you must confirm your administrator privileges by clicking **OK**.

        -   Windows CLI version
            1.  Open the Command Prompt.
            2.  Go to the directory where go2aliyun\_client.exe is located.
            3.  Run the `go2aliyun_client.exe -- onetime` command.
    -   For Linux servers, select how to run the SMC client based on whether you have root or sudo permissions on the source server system.
        -   In the directory where go2aliyun\_client is located, run the following commands as the root user:

            ``` {#codeblock_ufy_4so_jpa}
            chmod +x ./go2aliyun_client
            ```

            ``` {#codeblock_38g_164_grm}
            ./go2aliyun_client --onetime
            ```

        -   In the directory where go2aliyun\_client is located, run the following commands with sudo permissions:

            ``` {#codeblock_zdg_8wd_rd8}
            sudo chmod +x ./go2aliyun_client
            ```

            ``` {#codeblock_617_zpk_432}
            sudo ./go2aliyun_client --onetime
            ```

    You do not need to perform any other operations after you run the SMC client. Wait until the migration is complete. The client obtains source server information such as the number of CPU cores, memory size, memory usage, and CPU utilization, and displays the information on the client interface. The migration status is also displayed on the client interface as a log stream.


-   If the `Goto Aliyun Finished!` message is displayed, the migration succeeds, as shown in the following figure.

    ![go2Aliyun-success](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/668852/156630050550084_en-US.png)

    You can then perform the following operations:

    1.  Log on to the [ECS console](https://partners-intl.console.aliyun.com/#/ecs), and then select the target region to view the generated custom image.
    2.  [Use the custom image to create a pay-as-you-go instance](../reseller.en-US/Instances/Create an instance/Create an instance by using a custom image.md#) or [replace the system disk](../reseller.en-US/Block Storage/Block storage/Change the operating system/Replace the system disk (non-public image).md#) to check whether the custom image runs properly.

        **Note:** You can use a custom image without data disks to replace the system disk of the instance.

    3.  Remotely connect to the instance and check the migrated system. For more information, see [How can I check my system after migrating a Windows server?](../reseller.en-US/FAQ/SMC FAQ.md#section_c5i_33t_xn7) or [How can I check my system after migrating a Linux server?](../reseller.en-US/FAQ/SMC FAQ.md#section_8nx_71l_ksv).
-   If the `Goto Aliyun Not Finished!` message is displayed, the migration fails, as shown in the following figure.

    ![go2Aliyun-fail](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/668852/156630050650085_en-US.png)

    In this case, you must perform the following operations:

    1.  Check the error message in the log file under the Logs directory of the client to fix the error. For common problems and solutions, see [SMC FAQ](../reseller.en-US/FAQ/SMC FAQ.md#).
    2.  Re-run the SMC client. The client will resume from where the last migration left off.

        **Note:** If the intermediate instance is released, a new migration is required. For more information, see [What do I do if I released an intermediate instance by mistake?](../../../../../reseller.en-US/Migration Service/Cloud Migration tool for P2V and V2V/Cloud Migration tool FAQ.md#Release) and [When do I need to clear the client\_data file?](../../../../../reseller.en-US/Migration Service/Cloud Migration tool for P2V and V2V/Cloud Migration tool FAQ.md#ClearClient_data).



### 后端权限注解

1. 数据权限注解
    ```
        //传入权限字符串
        @RequiresPermissions('system:config:query')

        //传入权限字符串数组
        @RequiresPermissions(['system:config:query', 'system:config:list'])

        //传入的数组 取'并'
        @RequiresPermissions(['system:config:query', 'system:config:list'], LogicalEnum.and)

        //传入的数组 取'或' (默认)
        @RequiresPermissions(['system:config:query', 'system:config:list'], LogicalEnum.or)

    ```

1. 角色权限注解
    ```
        //传入角色标识
        @RequiresRoles('admin')

        //传入角色标识数组
        @RequiresRoles(['admin','test'])

        //传入角色标识数组 取'并'
        @RequiresRoles(['admin','test'], LogicalEnum.and)

        //传入角色标识数组 取'或' (默认)
        @RequiresRoles(['admin','test'], LogicalEnum.or)
    ```
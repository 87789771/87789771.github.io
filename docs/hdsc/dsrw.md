### 后端定时任务实现
1. 在app.module.ts 中 将所有将要被定时任务用到的 Providers 进行别名化，方便通过类名进行调用。
   ```
   // 以 JobService 为例，将其放入数组中即可
    const providers = [JobService]
    function createAliasProviders(): ExistingProvider[] {
        const aliasProviders: ExistingProvider[] = [];
        for (const p of providers) {
            aliasProviders.push({
            provide: p.name,
            useExisting: p,
            });
        }
        return aliasProviders;
    }
    const aliasProviders = createAliasProviders();
   ```
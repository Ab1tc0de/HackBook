# Mandatory Integrity Control
使用完整性级别来控制对安全对象的访问。当启动进程或创建对象时，它们接收执行此操作的主体的完整性级别。
`- System: SYSTEM (kernel, ...)`
`- High: Elevated users`
`- Medium: Standard users`
`- Low: Very restricted rights often used in sandboxed[^privesc_win_sandbox] processes or for directories storing temporary data`
`- Untrusted: Lowest integrity level with extremely limited access rights for processes or objects that pose the most potential risk`
使用进程控制器可以看到当前用户的完整性级别
使用icacls显示文件的完整性级别

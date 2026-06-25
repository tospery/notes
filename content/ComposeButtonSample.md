---
share: "true"
---
Compose Button 示例

```kotlin
package com.tospery.myandroid.samples  
  
import androidx.compose.foundation.layout.Arrangement  
import androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.Row  
import androidx.compose.foundation.layout.fillMaxSize  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.padding  
import androidx.compose.foundation.layout.size  
import androidx.compose.foundation.lazy.LazyColumn  
import androidx.compose.foundation.shape.RoundedCornerShape  
import androidx.compose.material.icons.Icons  
import androidx.compose.material.icons.filled.Add  
import androidx.compose.material.icons.filled.CheckCircle  
import androidx.compose.material.icons.filled.Refresh  
import androidx.compose.material3.Button  
import androidx.compose.material3.ButtonDefaults  
import androidx.compose.material3.CircularProgressIndicator  
import androidx.compose.material3.ElevatedButton  
import androidx.compose.material3.ElevatedCard  
import androidx.compose.material3.FilledTonalButton  
import androidx.compose.material3.Icon  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.OutlinedButton  
import androidx.compose.material3.Text  
import androidx.compose.material3.TextButton  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableIntStateOf  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.remember  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.Sample  
  
val ComposeButtonSample = Sample(  
    title = "Compose Button组件",  
    sourcePath = "ComposeButtonSample.kt",  
    content = { ComposeButtonSampleContent() })  
  
@Composable  
private fun ComposeButtonSampleContent() {  
    val onLog = LocalSampleLogger.current  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(), verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { ButtonVariantsCard(onLog = onLog) }  
        item { IconButtonContentCard(onLog = onLog) }  
        item { ButtonStateCard(onLog = onLog) }  
        item { LoadingButtonCard(onLog = onLog) }  
        item { ButtonStyleCard(onLog = onLog) }  
    }  
}  
  
@Composable  
private fun ButtonVariantsCard(  
    onLog: (String) -> Unit  
) {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp), verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "常见按钮类型",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "Material3 提供不同强调层级的按钮，用于不同优先级的动作。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Button(  
                onClick = {  
                    onLog("Compose Button组件: 点击 Button")  
                }) {  
                Text(text = "Button")  
            }  
            FilledTonalButton(  
                onClick = {  
                    onLog("Compose Button组件: 点击 FilledTonalButton")  
                }) {  
                Text(text = "FilledTonalButton")  
            }  
            ElevatedButton(  
                onClick = {  
                    onLog("Compose Button组件: 点击 ElevatedButton")  
                }) {  
                Text(text = "ElevatedButton")  
            }  
            OutlinedButton(  
                onClick = {  
                    onLog("Compose Button组件: 点击 OutlinedButton")  
                }) {  
                Text(text = "OutlinedButton")  
            }  
            TextButton(  
                onClick = {  
                    onLog("Compose Button组件: 点击 TextButton")  
                }) {  
                Text(text = "TextButton")  
            }  
        }  
    }  
}  
  
@Composable  
private fun IconButtonContentCard(  
    onLog: (String) -> Unit  
) {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp), verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "按钮内容", style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "Button 的 content 是 RowScope，可以自然放入 Icon 和 Text。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                horizontalArrangement = Arrangement.spacedBy(12.dp),  
                verticalAlignment = Alignment.CenterVertically  
            ) {  
                Button(  
                    onClick = {  
                        onLog("Compose Button组件: 点击添加按钮")  
                    }) {  
                    // 按钮文字已经表达动作，内部图标使用 null 避免读屏重复朗读。  
                    Icon(  
                        imageVector = Icons.Default.Add, contentDescription = null  
                    )  
                    Text(text = "添加")  
                }  
                OutlinedButton(  
                    onClick = {  
                        onLog("Compose Button组件: 点击刷新按钮")  
                    }) {  
                    Icon(  
                        imageVector = Icons.Default.Refresh, contentDescription = null  
                    )  
                    Text(text = "刷新")  
                }  
            }  
        }  
    }  
}  
  
@Composable  
private fun ButtonStateCard(  
    onLog: (String) -> Unit  
) {  
    var count by remember { mutableIntStateOf(0) }  
    val canSubmit = count >= 3  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "启用与禁用状态",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "enabled=false 会禁用点击、涟漪和语义动作，适合表达暂时不可用。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Text(  
                text = "当前进度: $count / 3",  
                style = MaterialTheme.typography.bodyLarge  
            )  
            Row(  
                horizontalArrangement = Arrangement.spacedBy(12.dp),  
                verticalAlignment = Alignment.CenterVertically  
            ) {  
                Button(  
                    onClick = {  
                        count += 1  
                        onLog("Compose Button组件: 进度增加到 $count")  
                    }, enabled = count < 3  
                ) {  
                    Text(text = "增加进度")  
                }  
                Button(  
                    onClick = {  
                        onLog("Compose Button组件: 提交成功")  
                    }, enabled = canSubmit  
                ) {  
                    Text(text = "提交")  
                }  
                TextButton(  
                    onClick = {  
                        count = 0  
                        onLog("Compose Button组件: 重置进度")  
                    }) {  
                    Text(text = "重置")  
                }  
            }  
        }  
    }  
}  
  
@Composable  
private fun LoadingButtonCard(  
    onLog: (String) -> Unit  
) {  
    var loading by remember { mutableStateOf(false) }  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "加载态按钮",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "加载时通常禁用按钮，并在内容区域展示进度反馈。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Button(  
                onClick = {  
                    loading = true  
                    onLog("Compose Button组件: 开始加载")  
                },  
                modifier = Modifier.fillMaxWidth(),  
                enabled = !loading  
            ) {  
                if (loading) {  
                    CircularProgressIndicator(  
                        modifier = Modifier.size(18.dp),  
                        strokeWidth = 2.dp,  
                        color = MaterialTheme.colorScheme.onPrimary  
                    )  
                    Text(text = "加载中")  
                } else {  
                    Icon(  
                        imageVector = Icons.Default.CheckCircle,  
                        contentDescription = null  
                    )  
                    Text(text = "开始请求")  
                }  
            }  
            OutlinedButton(  
                onClick = {  
                    loading = false  
                    onLog("Compose Button组件: 取消加载")  
                }, enabled = loading  
            ) {  
                Text(text = "取消")  
            }  
        }  
    }  
}  
  
@Composable  
private fun ButtonStyleCard(  
    onLog: (String) -> Unit  
) {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "样式定制",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "通过 colors、shape、contentPadding 等参数微调按钮外观。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Button(  
                onClick = {  
                    onLog("Compose Button组件: 点击圆角强调按钮")  
                }, shape = RoundedCornerShape(12.dp), colors = ButtonDefaults.buttonColors(  
                    containerColor = MaterialTheme.colorScheme.tertiary,  
                    contentColor = MaterialTheme.colorScheme.onTertiary  
                )  
            ) {  
                Text(text = "圆角强调")  
            }  
            OutlinedButton(  
                onClick = {  
                    onLog("Compose Button组件: 点击宽间距按钮")  
                },  
                modifier = Modifier.fillMaxWidth(),  
                contentPadding = ButtonDefaults.ButtonWithIconContentPadding  
            ) {  
                Icon(  
                    imageVector = Icons.Default.CheckCircle, contentDescription = null  
                )  
                Text(text = "全宽按钮")  
            }  
        }  
    }  
}
```


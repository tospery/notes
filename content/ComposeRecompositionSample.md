---
share: "true"
---
Compose重组

```kotlin
package com.tospery.myandroid.samples  
  
import androidx.compose.foundation.layout.Arrangement  
import androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.Row  
import androidx.compose.foundation.layout.fillMaxSize  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.padding  
import androidx.compose.foundation.lazy.LazyColumn  
import androidx.compose.material3.Button  
import androidx.compose.material3.ElevatedCard  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.OutlinedButton  
import androidx.compose.material3.OutlinedTextField  
import androidx.compose.material3.Switch  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.SideEffect  
import androidx.compose.runtime.derivedStateOf  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.remember  
import androidx.compose.runtime.saveable.rememberSaveable  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.Sample  
  
/**  
 * Compose 重组（Recomposition）示例。  
 *  
 * 演示要点：  
 * 1. 读取 State 的 Composable 会在 State 变化后进入重组。  
 * 2. Compose 会尽量跳过参数未变化的子组件，缩小重组范围。  
 * 3. remember 能把对象保存在 Composition 中，避免每次重组都重新创建。  
 * 4. derivedStateOf 适合从已有状态派生结果，减少无意义的下游更新。  
 */  
val ComposeRecompositionSample = Sample(  
    title = "Compose 重组",  
    sourcePath = "ComposeRecompositionSample.kt",  
    content = { ComposeRecompositionSampleContent() }  
)  
  
private class RecomposeCounter {  
    var count: Int = 0  
}  
  
@Composable  
private fun ComposeRecompositionSampleContent() {  
    val onLog = LocalSampleLogger.current  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(),  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { StateInvalidationCard(onLog = onLog) }  
        item { RecompositionScopeCard(onLog = onLog) }  
        item { DerivedStateCard(onLog = onLog) }  
    }  
}  
  
@Composable  
private fun StateInvalidationCard(  
    onLog: (String) -> Unit  
) {  
    var count by rememberSaveable { mutableStateOf(0) }  
    var keyword by rememberSaveable { mutableStateOf("Compose") }  
    SampleCard(  
        title = "状态变化触发重组",  
        desc = "当 count 或 keyword 改变时，读取它们的区域会重新执行。SideEffect 用来观察一次成功重组。"  
    ) {  
        RecomposeBadge(label = "当前卡片")  
        Text(  
            text = "计数: $count",  
            style = MaterialTheme.typography.headlineSmall  
        )  
        OutlinedTextField(  
            value = keyword,  
            onValueChange = { input ->  
                keyword = input.take(16)  
                onLog("Compose 重组: keyword = $keyword")  
            },  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "关键字") },  
            singleLine = true  
        )  
        Row(  
            horizontalArrangement = Arrangement.spacedBy(10.dp),  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            Button(  
                onClick = {  
                    count += 1  
                    onLog("Compose 重组: count = $count")  
                }  
            ) {  
                Text(text = "改变状态")  
            }  
            OutlinedButton(  
                onClick = {  
                    count = 0  
                    keyword = "Compose"  
                    onLog("Compose 重组: 状态已重置")  
                }  
            ) {  
                Text(text = "重置")  
            }  
        }  
    }  
}  
  
@Composable  
private fun RecompositionScopeCard(  
    onLog: (String) -> Unit  
) {  
    var count by rememberSaveable { mutableStateOf(0) }  
    var enabled by rememberSaveable { mutableStateOf(true) }  
    SampleCard(  
        title = "重组范围与跳过",  
        desc = "两个子组件分别读取不同状态。只改变 count 时，开关子组件的参数不变，Compose 可以跳过它。"  
    ) {  
        CounterReader(value = count)  
        SwitchReader(  
            enabled = enabled,  
            onEnabledChange = { checked ->  
                enabled = checked  
                onLog("Compose 重组: enabled = $enabled")  
            }  
        )  
        Row(  
            horizontalArrangement = Arrangement.spacedBy(10.dp),  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            Button(  
                onClick = {  
                    count += 1  
                    onLog("Compose 重组: 范围示例 count = $count")  
                }  
            ) {  
                Text(text = "只改变计数")  
            }  
            OutlinedButton(  
                onClick = {  
                    count = 0  
                    enabled = true  
                    onLog("Compose 重组: 范围示例已重置")  
                }  
            ) {  
                Text(text = "重置")  
            }  
        }  
    }  
}  
  
@Composable  
private fun CounterReader(  
    value: Int  
) {  
    Column(  
        verticalArrangement = Arrangement.spacedBy(6.dp)  
    ) {  
        RecomposeBadge(label = "计数组件")  
        Text(  
            text = "读取 count: $value",  
            style = MaterialTheme.typography.bodyLarge  
        )  
    }  
}  
  
@Composable  
private fun SwitchReader(  
    enabled: Boolean,  
    onEnabledChange: (Boolean) -> Unit  
) {  
    Row(  
        modifier = Modifier.fillMaxWidth(),  
        horizontalArrangement = Arrangement.SpaceBetween,  
        verticalAlignment = Alignment.CenterVertically  
    ) {  
        Column(  
            verticalArrangement = Arrangement.spacedBy(6.dp)  
        ) {  
            RecomposeBadge(label = "开关组件")  
            Text(  
                text = if (enabled) "开关状态: 开" else "开关状态: 关",  
                style = MaterialTheme.typography.bodyLarge  
            )  
        }  
        Switch(  
            checked = enabled,  
            onCheckedChange = onEnabledChange  
        )  
    }  
}  
  
@Composable  
private fun DerivedStateCard(  
    onLog: (String) -> Unit  
) {  
    var rawText by rememberSaveable { mutableStateOf("") }  
    var showHint by rememberSaveable { mutableStateOf(true) }  
    val normalizedText by remember {  
        derivedStateOf {  
            rawText.trim().lowercase()  
        }  
    }  
    SampleCard(  
        title = "remember 与 derivedStateOf",  
        desc = "rawText 改变时才会得到新的派生文本；切换提示开关不会改变 normalizedText。"  
    ) {  
        OutlinedTextField(  
            value = rawText,  
            onValueChange = { input ->  
                rawText = input.take(24)  
                onLog("Compose 重组: rawText length = ${rawText.length}")  
            },  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "输入内容") },  
            singleLine = true  
        )  
        NormalizedText(value = normalizedText)  
        Row(  
            modifier = Modifier.fillMaxWidth(),  
            horizontalArrangement = Arrangement.SpaceBetween,  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            Text(  
                text = if (showHint) {  
                    "提示开启: 试着只切换开关，观察派生文本组件。"  
                } else {  
                    "提示关闭"  
                },  
                modifier = Modifier.weight(1f),  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Switch(  
                checked = showHint,  
                onCheckedChange = { checked ->  
                    showHint = checked  
                    onLog("Compose 重组: showHint = $showHint")  
                }  
            )  
        }  
    }  
}  
  
@Composable  
private fun NormalizedText(  
    value: String  
) {  
    Column(  
        verticalArrangement = Arrangement.spacedBy(6.dp)  
    ) {  
        RecomposeBadge(label = "派生文本组件")  
        Text(  
            text = "标准化结果: ${value.ifBlank { "空" }}",  
            style = MaterialTheme.typography.bodyLarge  
        )  
    }  
}  
  
@Composable  
private fun RecomposeBadge(  
    label: String  
) {  
    val counter = remember { RecomposeCounter() }  
    SideEffect {  
        counter.count += 1  
    }  
    Text(  
        text = "$label 已完成重组: ${counter.count} 次",  
        style = MaterialTheme.typography.labelLarge,  
        color = MaterialTheme.colorScheme.primary  
    )  
}  
  
@Composable  
private fun SampleCard(  
    title: String,  
    desc: String,  
    content: @Composable () -> Unit  
) {  
    ElevatedCard(modifier = Modifier.fillMaxWidth()) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = title,  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = desc,  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            content()  
        }  
    }  
}
```


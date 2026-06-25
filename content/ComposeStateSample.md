---
share: "true"
---
Compose状态

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
import androidx.compose.material3.FilledTonalButton  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.OutlinedButton  
import androidx.compose.material3.OutlinedTextField  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.derivedStateOf  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableStateListOf  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.remember  
import androidx.compose.runtime.saveable.rememberSaveable  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.text.style.TextDecoration  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.Sample  
  
/**  
 * Compose 状态（State）示例。  
 *  
 * 演示要点：  
 * 1. remember + mutableStateOf：让状态在重组之间保持。  
 * 2. remember vs rememberSaveable：屏幕旋转后是否还能恢复。  
 * 3. 状态提升（State Hoisting）：无状态子组件 + 受控数据流。  
 * 4. derivedStateOf：从已有状态派生新状态，避免无谓重组。  
 * 5. mutableStateListOf：可观察集合，增删元素自动刷新 UI。  
 *  
 * 核心理念：UI = f(state)，改变状态即可驱动界面重组。  
 */  
val ComposeStateSample = Sample(  
    title = "Compose 状态(State)",  
    sourcePath = "ComposeStateSample.kt",  
    content = { ComposeStateSampleContent() }  
)  
  
@Composable  
private fun ComposeStateSampleContent() {  
    val onLog = LocalSampleLogger.current  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(),  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { RememberBasicsCard(onLog = onLog) }  
        item { SaveableCard(onLog = onLog) }  
        item { HoistingCard(onLog = onLog) }  
        item { DerivedStateCard(onLog = onLog) }  
        item { StateListCard(onLog = onLog) }  
    }  
}  
  
/**  
 * 1、remember + mutableStateOf：最基础的可观察状态。  
 *  
 * `var count by remember { mutableStateOf(0) }` 把状态记入 Composition，  
 * 重组时不会被重新初始化为 0；对 count 赋值会触发读取它的 Composable 重组。  
 */  
@Composable  
private fun RememberBasicsCard(  
    onLog: (String) -> Unit  
) {  
    var count by remember { mutableStateOf(0) }  
    SampleCard(  
        title = "remember + mutableStateOf",  
        desc = "状态记入 Composition，重组时保持；写入即触发重组。"  
    ) {  
        Text(  
            text = "当前计数: $count",  
            style = MaterialTheme.typography.headlineSmall  
        )  
        Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {  
            Button(  
                onClick = {  
                    count++  
                    onLog("Compose 状态(State): count = $count")  
                }  
            ) {  
                Text(text = "加 1")  
            }  
            OutlinedButton(  
                onClick = {  
                    count = 0  
                    onLog("Compose 状态(State): 计数已重置")  
                }  
            ) {  
                Text(text = "重置")  
            }  
        }  
    }  
}  
  
/**  
 * 2、remember vs rememberSaveable：跨配置变更（如屏幕旋转）是否保留。  
 *  
 * remember 只在重组间保持，旋转屏幕会丢失；  
 * rememberSaveable 会把值存进 Bundle，旋转 / 进程恢复后仍保留。  
 * 旋转设备观察两个数字的差异即可理解区别。  
 */  
@Composable  
private fun SaveableCard(  
    onLog: (String) -> Unit  
) {  
    var transient by remember { mutableStateOf(0) }  
    var persisted by rememberSaveable { mutableStateOf(0) }  
    SampleCard(  
        title = "remember vs rememberSaveable",  
        desc = "旋转屏幕后：remember 归零，rememberSaveable 保留。"  
    ) {  
        Text(text = "remember(易失): $transient")  
        Text(text = "rememberSaveable(可恢复): $persisted")  
        Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {  
            FilledTonalButton(  
                onClick = {  
                    transient++  
                    persisted++  
                    onLog("Compose 状态(State): transient=$transient persisted=$persisted")  
                }  
            ) {  
                Text(text = "两者都 +1")  
            }  
        }  
    }  
}  
  
/**  
 * 3、状态提升（State Hoisting）。  
 *  
 * StatelessCounter 不持有任何状态，只接收 value 和回调（单向数据流），  
 * 因此可复用、可测试、可预览。真实状态由调用方 HoistingCard 持有。  
 */  
@Composable  
private fun HoistingCard(  
    onLog: (String) -> Unit  
) {  
    // 状态被提升到调用方  
    var likes by remember { mutableStateOf(0) }  
    SampleCard(  
        title = "状态提升 (State Hoisting)",  
        desc = "子组件无状态(value + onValueChange)，状态由父级持有。"  
    ) {  
        StatelessCounter(  
            value = likes,  
            onIncrement = {  
                likes++  
                onLog("Compose 状态(State): 点赞数 = $likes")  
            }  
        )  
    }  
}  
  
/** 无状态子组件：只读 value、只通过回调请求改变，自身不持有任何 state。 */@Composable  
private fun StatelessCounter(  
    value: Int,  
    onIncrement: () -> Unit  
) {  
    Row(  
        verticalAlignment = Alignment.CenterVertically,  
        horizontalArrangement = Arrangement.spacedBy(12.dp)  
    ) {  
        Text(  
            text = "👍 $value",  
            style = MaterialTheme.typography.titleLarge  
        )  
        Button(onClick = onIncrement) {  
            Text(text = "点赞")  
        }  
    }  
}  
  
/**  
 * 4、derivedStateOf：从已有状态派生新状态。  
 *  
 * isValid 完全由 name 与 age 计算得出。用 derivedStateOf 包裹，  
 * 仅当派生结果真正变化时才通知下游，避免每次输入都做无谓重组。  
 */  
@Composable  
private fun DerivedStateCard(  
    onLog: (String) -> Unit  
) {  
    var name by rememberSaveable { mutableStateOf("") }  
    var age by rememberSaveable { mutableStateOf("") }  
    // 派生状态：随 name / age 自动计算  
    val isValid by remember {  
        derivedStateOf {  
            name.isNotBlank() && (age.toIntOrNull() ?: 0) in 1..150  
        }  
    }  
    SampleCard(  
        title = "派生状态 derivedStateOf",  
        desc = "表单是否有效由输入派生，结果变化时才通知下游。"  
    ) {  
        OutlinedTextField(  
            value = name,  
            onValueChange = { name = it.take(16) },  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "姓名") },  
            singleLine = true  
        )  
        OutlinedTextField(  
            value = age,  
            onValueChange = { input -> age = input.filter(Char::isDigit).take(3) },  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "年龄") },  
            singleLine = true  
        )  
        Button(  
            onClick = { onLog("Compose 状态(State): 提交 name=$name age=$age") },  
            enabled = isValid  
        ) {  
            Text(text = if (isValid) "可以提交" else "请完善信息")  
        }  
    }  
}  
  
/**  
 * 5、mutableStateListOf：可观察集合。  
 *  
 * 普通 List 改了内容 UI 不会刷新；mutableStateListOf 对增删改都可观察，  
 * 自动触发依赖它的 Composable 重组。  
 */  
@Composable  
private fun StateListCard(  
    onLog: (String) -> Unit  
) {  
    val todos = remember { mutableStateListOf("学习 remember", "理解状态提升") }  
    var input by remember { mutableStateOf("") }  
    SampleCard(  
        title = "可观察集合 mutableStateListOf",  
        desc = "对集合增删元素会自动刷新列表 UI。"  
    ) {  
        Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {  
            OutlinedTextField(  
                value = input,  
                onValueChange = { input = it.take(20) },  
                modifier = Modifier.weight(1f),  
                label = { Text(text = "新待办") },  
                singleLine = true  
            )  
            Button(  
                onClick = {  
                    if (input.isNotBlank()) {  
                        todos.add(input.trim())  
                        onLog("Compose 状态(State): 新增待办 '$input'，共 ${todos.size} 项")  
                        input = ""  
                    }  
                }  
            ) {  
                Text(text = "添加")  
            }  
        }  
        todos.forEachIndexed { index, todo ->  
            Row(  
                modifier = Modifier.fillMaxWidth(),  
                verticalAlignment = Alignment.CenterVertically,  
                horizontalArrangement = Arrangement.SpaceBetween  
            ) {  
                Text(  
                    text = "• $todo",  
                    style = MaterialTheme.typography.bodyLarge.copy(  
                        textDecoration = TextDecoration.None  
                    )  
                )  
                OutlinedButton(  
                    onClick = {  
                        onLog("Compose 状态(State): 删除待办 '$todo'")  
                        todos.removeAt(index)  
                    }  
                ) {  
                    Text(text = "删除")  
                }  
            }  
        }  
    }  
}  
  
/** 统一的示例卡片外壳：标题 + 说明 + 内容。 */@Composable  
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


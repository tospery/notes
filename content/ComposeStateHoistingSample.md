---
share: "true"
---
Compose状态提升

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
import androidx.compose.material3.Slider  
import androidx.compose.material3.Switch  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.saveable.rememberSaveable  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.Sample  
  
/**  
 * Compose 状态提升（State Hoisting）示例。  
 *  
 * 演示要点：  
 * 1. 父组件作为单一数据源持有状态。  
 * 2. 子组件保持无状态，只接收 value 和 onValueChange。  
 * 3. 多个子组件共享同一份状态时，把状态提升到共同父级。  
 * 4. 只有父级真正关心的状态才需要提升，纯局部 UI 状态可以留在内部。  
 */  
val ComposeStateHoistingSample = Sample(  
    title = "Compose 状态提升",  
    sourcePath = "ComposeStateHoistingSample.kt",  
    content = { ComposeStateHoistingSampleContent() }  
)  
  
private data class LearningProfile(  
    val name: String,  
    val dailyGoal: String,  
    val reminderEnabled: Boolean,  
    val progress: Float  
)  
  
@Composable  
private fun ComposeStateHoistingSampleContent() {  
    val onLog = LocalSampleLogger.current  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(),  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { ProfileHoistingCard(onLog = onLog) }  
        item { SharedCounterCard(onLog = onLog) }  
        item { LocalStateBoundaryCard(onLog = onLog) }  
    }  
}  
  
@Composable  
private fun ProfileHoistingCard(  
    onLog: (String) -> Unit  
) {  
    var name by rememberSaveable { mutableStateOf("小林") }  
    var dailyGoal by rememberSaveable { mutableStateOf("30") }  
    var reminderEnabled by rememberSaveable { mutableStateOf(true) }  
    var progress by rememberSaveable { mutableStateOf(40f) }  
    val profile = LearningProfile(  
        name = name,  
        dailyGoal = dailyGoal,  
        reminderEnabled = reminderEnabled,  
        progress = progress  
    )  
    SampleCard(  
        title = "父组件持有状态",  
        desc = "ProfileEditor 不保存输入内容，只展示 profile，并把用户操作通过回调交回父组件。"  
    ) {  
        ProfileEditor(  
            profile = profile,  
            onNameChange = { input ->  
                name = input.take(12)  
                onLog("Compose 状态提升: name = $name")  
            },  
            onDailyGoalChange = { input ->  
                dailyGoal = input.filter(Char::isDigit).take(3)  
                onLog("Compose 状态提升: dailyGoal = $dailyGoal")  
            },  
            onReminderChange = { enabled ->  
                reminderEnabled = enabled  
                onLog("Compose 状态提升: reminderEnabled = $reminderEnabled")  
            },  
            onProgressChange = { value ->  
                progress = value  
            },  
            onProgressChangeFinished = {  
                onLog("Compose 状态提升: progress = ${progress.toInt()}%")  
            },  
            onResetClick = {  
                name = "小林"  
                dailyGoal = "30"  
                reminderEnabled = true  
                progress = 40f  
                onLog("Compose 状态提升: 资料已由父组件重置")  
            }  
        )  
    }  
}  
  
@Composable  
private fun ProfileEditor(  
    profile: LearningProfile,  
    onNameChange: (String) -> Unit,  
    onDailyGoalChange: (String) -> Unit,  
    onReminderChange: (Boolean) -> Unit,  
    onProgressChange: (Float) -> Unit,  
    onProgressChangeFinished: () -> Unit,  
    onResetClick: () -> Unit  
) {  
    Column(  
        verticalArrangement = Arrangement.spacedBy(12.dp)  
    ) {  
        OutlinedTextField(  
            value = profile.name,  
            onValueChange = onNameChange,  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "学习者") },  
            singleLine = true  
        )  
        OutlinedTextField(  
            value = profile.dailyGoal,  
            onValueChange = onDailyGoalChange,  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "每日目标(分钟)") },  
            singleLine = true  
        )  
        Row(  
            modifier = Modifier.fillMaxWidth(),  
            horizontalArrangement = Arrangement.SpaceBetween,  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            Text(  
                text = "每日提醒",  
                style = MaterialTheme.typography.bodyLarge  
            )  
            Switch(  
                checked = profile.reminderEnabled,  
                onCheckedChange = onReminderChange  
            )  
        }  
        Column(  
            verticalArrangement = Arrangement.spacedBy(4.dp)  
        ) {  
            Text(  
                text = "完成度: ${profile.progress.toInt()}%",  
                style = MaterialTheme.typography.bodyLarge  
            )  
            Slider(  
                value = profile.progress,  
                onValueChange = onProgressChange,  
                onValueChangeFinished = onProgressChangeFinished,  
                valueRange = 0f..100f,  
                steps = 4  
            )  
        }  
        FilledTonalButton(onClick = onResetClick) {  
            Text(text = "父组件重置全部状态")  
        }  
    }  
}  
  
@Composable  
private fun SharedCounterCard(  
    onLog: (String) -> Unit  
) {  
    var count by rememberSaveable { mutableStateOf(0) }  
    SampleCard(  
        title = "共同父级作为单一数据源",  
        desc = "两个子组件同时读写 count，所以 count 被提升到它们共同的父组件中。"  
    ) {  
        CounterReadout(value = count)  
        CounterControls(  
            value = count,  
            onIncrement = {  
                count += 1  
                onLog("Compose 状态提升: count = $count")  
            },  
            onDecrement = {  
                count -= 1  
                onLog("Compose 状态提升: count = $count")  
            },  
            onReset = {  
                count = 0  
                onLog("Compose 状态提升: count 已重置")  
            }  
        )  
    }  
}  
  
@Composable  
private fun CounterReadout(  
    value: Int  
) {  
    Text(  
        text = "当前计数: $value",  
        style = MaterialTheme.typography.headlineSmall  
    )  
}  
  
@Composable  
private fun CounterControls(  
    value: Int,  
    onIncrement: () -> Unit,  
    onDecrement: () -> Unit,  
    onReset: () -> Unit  
) {  
    Row(  
        horizontalArrangement = Arrangement.spacedBy(10.dp),  
        verticalAlignment = Alignment.CenterVertically  
    ) {  
        Button(onClick = onIncrement) {  
            Text(text = "加 1")  
        }  
        OutlinedButton(  
            onClick = onDecrement,  
            enabled = value > 0  
        ) {  
            Text(text = "减 1")  
        }  
        OutlinedButton(onClick = onReset) {  
            Text(text = "重置")  
        }  
    }  
}  
  
@Composable  
private fun LocalStateBoundaryCard(  
    onLog: (String) -> Unit  
) {  
    SampleCard(  
        title = "不必提升所有状态",  
        desc = "父组件不关心的展开/收起状态，可以留在子组件内部；真正影响业务或兄弟组件的数据再提升。"  
    ) {  
        ExpandableLearningTip(onLog = onLog)  
    }  
}  
  
@Composable  
private fun ExpandableLearningTip(  
    onLog: (String) -> Unit  
) {  
    var expanded by rememberSaveable { mutableStateOf(false) }  
    Column(  
        verticalArrangement = Arrangement.spacedBy(10.dp)  
    ) {  
        Text(  
            text = if (expanded) {  
                "这个展开状态只影响当前提示块，父组件不需要读取它，所以留在内部更简单。"  
            } else {  
                "当前提示已收起。"  
            },  
            style = MaterialTheme.typography.bodyLarge  
        )  
        OutlinedButton(  
            onClick = {  
                expanded = !expanded  
                onLog("Compose 状态提升: 局部提示 expanded = $expanded")  
            }  
        ) {  
            Text(text = if (expanded) "收起提示" else "展开提示")  
        }  
    }  
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


---
share: "true"
---
Compose TextField组件

```kotlin
package com.tospery.myandroid.samples  
  
import androidx.compose.foundation.background  
import androidx.compose.foundation.border  
import androidx.compose.foundation.layout.Arrangement  
import androidx.compose.foundation.layout.Box  
import androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.Row  
import androidx.compose.foundation.layout.fillMaxSize  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.padding  
import androidx.compose.foundation.lazy.LazyColumn  
import androidx.compose.foundation.shape.RoundedCornerShape  
import androidx.compose.foundation.text.BasicTextField  
import androidx.compose.foundation.text.KeyboardActions  
import androidx.compose.foundation.text.KeyboardOptions  
import androidx.compose.material.icons.Icons  
import androidx.compose.material.icons.filled.Clear  
import androidx.compose.material.icons.filled.Visibility  
import androidx.compose.material.icons.filled.VisibilityOff  
import androidx.compose.material3.AssistChip  
import androidx.compose.material3.ElevatedCard  
import androidx.compose.material3.Icon  
import androidx.compose.material3.IconButton  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.OutlinedTextField  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.remember  
import androidx.compose.runtime.saveable.rememberSaveable  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.focus.FocusRequester  
import androidx.compose.ui.focus.focusRequester  
import androidx.compose.ui.graphics.SolidColor  
import androidx.compose.ui.platform.LocalFocusManager  
import androidx.compose.ui.text.input.ImeAction  
import androidx.compose.ui.text.input.KeyboardCapitalization  
import androidx.compose.ui.text.input.KeyboardType  
import androidx.compose.ui.text.input.PasswordVisualTransformation  
import androidx.compose.ui.text.input.VisualTransformation  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.Sample  
  
val ComposeTextFieldSample = Sample(  
    title = "Compose TextField组件",  
    sourcePath = "ComposeTextFieldSample.kt",  
    content = { ComposeTextFieldSampleContent() }  
)  
  
@Composable  
private fun ComposeTextFieldSampleContent() {  
    val onLog = LocalSampleLogger.current  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(),  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { AccountFormCard(onLog = onLog) }  
        item { PasswordFieldCard(onLog = onLog) }  
        item { MultiLineFieldCard(onLog = onLog) }  
        item { BasicTextFieldCard(onLog = onLog) }  
    }  
}  
  
@Composable  
private fun AccountFormCard(  
    onLog: (String) -> Unit  
) {  
    var username by rememberSaveable { mutableStateOf("") }  
    var phone by rememberSaveable { mutableStateOf("") }  
    val focusManager = LocalFocusManager.current  
    val phoneFocusRequester = remember { FocusRequester() }  
    val usernameError = username.isNotEmpty() && username.length < 3  
    val phoneError = phone.isNotEmpty() && phone.length < 11  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "表单输入与校验",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "value 保存输入内容，onValueChange 同步更新状态，isError 和 supportingText 负责错误提示。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            OutlinedTextField(  
                value = username,  
                onValueChange = { newValue ->  
                    username = newValue.take(20)  
                    onLog("Compose TextField组件: 用户名长度 ${username.length}")  
                },  
                modifier = Modifier.fillMaxWidth(),  
                label = { Text(text = "用户名") },  
                placeholder = { Text(text = "至少 3 个字符") },  
                singleLine = true,  
                isError = usernameError,  
                supportingText = {  
                    Text(  
                        text = if (usernameError) {  
                            "用户名至少需要 3 个字符"  
                        } else {  
                            "${username.length}/20"  
                        }  
                    )  
                },  
                trailingIcon = {  
                    if (username.isNotEmpty()) {  
                        IconButton(  
                            onClick = {  
                                username = ""  
                                onLog("Compose TextField组件: 清空用户名")  
                            }  
                        ) {  
                            Icon(  
                                imageVector = Icons.Default.Clear,  
                                contentDescription = "清空用户名"  
                            )  
                        }  
                    }  
                },  
                keyboardOptions = KeyboardOptions(  
                    capitalization = KeyboardCapitalization.None,  
                    keyboardType = KeyboardType.Text,  
                    imeAction = ImeAction.Next  
                ),  
                keyboardActions = KeyboardActions(  
                    onNext = {  
                        phoneFocusRequester.requestFocus()  
                    }  
                )  
            )  
            OutlinedTextField(  
                value = phone,  
                onValueChange = { newValue ->  
                    phone = newValue.filter(Char::isDigit).take(11)  
                    onLog("Compose TextField组件: 手机号长度 ${phone.length}")  
                },  
                modifier = Modifier  
                    .fillMaxWidth()  
                    .focusRequester(phoneFocusRequester),  
                label = { Text(text = "手机号") },  
                placeholder = { Text(text = "仅允许输入数字") },  
                singleLine = true,  
                isError = phoneError,  
                supportingText = {  
                    Text(  
                        text = if (phoneError) {  
                            "请输入 11 位手机号"  
                        } else {  
                            "Input filter: 只保留数字并限制长度"  
                        }  
                    )  
                },  
                keyboardOptions = KeyboardOptions(  
                    keyboardType = KeyboardType.Phone,  
                    imeAction = ImeAction.Done  
                ),  
                keyboardActions = KeyboardActions(  
                    onDone = {  
                        focusManager.clearFocus()  
                        onLog("Compose TextField组件: 完成手机号输入 $phone")  
                    }  
                )  
            )  
        }  
    }  
}  
  
@Composable  
private fun PasswordFieldCard(  
    onLog: (String) -> Unit  
) {  
    var password by rememberSaveable { mutableStateOf("") }  
    var passwordVisible by rememberSaveable { mutableStateOf(false) }  
    val passwordStrength = when {  
        password.length >= 12 -> "强"  
        password.length >= 8 -> "中"  
        password.isNotEmpty() -> "弱"  
        else -> "未输入"  
    }  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "密码输入",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "通过 visualTransformation 控制显示内容，真实状态仍然保存原始密码字符串。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            OutlinedTextField(  
                value = password,  
                onValueChange = { newValue ->  
                    password = newValue  
                    onLog("Compose TextField组件: 密码长度 ${password.length}")  
                },  
                modifier = Modifier.fillMaxWidth(),  
                label = { Text(text = "密码") },  
                singleLine = true,  
                visualTransformation = if (passwordVisible) {  
                    VisualTransformation.None  
                } else {  
                    PasswordVisualTransformation()  
                },  
                trailingIcon = {  
                    IconButton(  
                        onClick = {  
                            passwordVisible = !passwordVisible  
                            onLog("Compose TextField组件: 密码可见 $passwordVisible")  
                        }  
                    ) {  
                        Icon(  
                            imageVector = if (passwordVisible) {  
                                Icons.Default.VisibilityOff  
                            } else {  
                                Icons.Default.Visibility  
                            },  
                            contentDescription = if (passwordVisible) {  
                                "隐藏密码"  
                            } else {  
                                "显示密码"  
                            }  
                        )  
                    }  
                },  
                keyboardOptions = KeyboardOptions(  
                    keyboardType = KeyboardType.Password,  
                    imeAction = ImeAction.Done  
                ),  
                supportingText = {  
                    Text(text = "密码强度: $passwordStrength")  
                }  
            )  
        }  
    }  
}  
  
@Composable  
private fun MultiLineFieldCard(  
    onLog: (String) -> Unit  
) {  
    var message by rememberSaveable {  
        mutableStateOf("Compose TextField 支持单行、多行、错误态和键盘动作。")  
    }  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "多行输入",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "多行输入适合备注、评论和描述类内容，minLines 与 maxLines 可以约束输入区域高度。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            OutlinedTextField(  
                value = message,  
                onValueChange = { newValue ->  
                    message = newValue.take(120)  
                    onLog("Compose TextField组件: 备注长度 ${message.length}")  
                },  
                modifier = Modifier.fillMaxWidth(),  
                label = { Text(text = "备注") },  
                minLines = 3,  
                maxLines = 5,  
                supportingText = {  
                    Text(text = "${message.length}/120")  
                },  
                keyboardOptions = KeyboardOptions(  
                    capitalization = KeyboardCapitalization.Sentences,  
                    keyboardType = KeyboardType.Text  
                )  
            )  
            Row(  
                horizontalArrangement = Arrangement.spacedBy(10.dp),  
                verticalAlignment = Alignment.CenterVertically  
            ) {  
                AssistChip(  
                    onClick = {  
                        message = ""  
                        onLog("Compose TextField组件: 清空备注")  
                    },  
                    label = { Text(text = "清空") }  
                )  
                AssistChip(  
                    onClick = {  
                        message = "已选择模板: 请在这里补充详细说明。"  
                        onLog("Compose TextField组件: 填入备注模板")  
                    },  
                    label = { Text(text = "填入模板") }  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun BasicTextFieldCard(  
    onLog: (String) -> Unit  
) {  
    var nickname by rememberSaveable { mutableStateOf("") }  
    val shape = RoundedCornerShape(14.dp)  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "BasicTextField 自定义外观",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "BasicTextField 只处理文本编辑能力，边框、背景、占位内容都由外层组合出来。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            BasicTextField(  
                value = nickname,  
                onValueChange = { newValue ->  
                    nickname = newValue.take(12)  
                    onLog("Compose TextField组件: 昵称长度 ${nickname.length}")  
                },  
                modifier = Modifier  
                    .fillMaxWidth()  
                    .border(  
                        width = 1.dp,  
                        color = MaterialTheme.colorScheme.outline,  
                        shape = shape  
                    )  
                    .background(  
                        color = MaterialTheme.colorScheme.surfaceVariant,  
                        shape = shape  
                    )  
                    .padding(horizontal = 16.dp, vertical = 14.dp),  
                singleLine = true,  
                textStyle = MaterialTheme.typography.bodyLarge.copy(  
                    color = MaterialTheme.colorScheme.onSurface  
                ),  
                cursorBrush = SolidColor(MaterialTheme.colorScheme.primary),  
                keyboardOptions = KeyboardOptions(  
                    imeAction = ImeAction.Done  
                ),  
                decorationBox = { innerTextField ->  
                    Box(modifier = Modifier.fillMaxWidth()) {  
                        if (nickname.isEmpty()) {  
                            Text(  
                                text = "输入自定义昵称",  
                                style = MaterialTheme.typography.bodyLarge,  
                                color = MaterialTheme.colorScheme.onSurfaceVariant  
                            )  
                        }  
                        innerTextField()  
                    }  
                }  
            )  
            Text(  
                text = "当前昵称: ${nickname.ifBlank { "未填写" }}",  
                style = MaterialTheme.typography.bodyMedium  
            )  
        }  
    }  
}
```



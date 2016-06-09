Encrypting Settings in App Config
=================================

[Source:  http://weblogs.asp.net/jongalloway/encrypting-passwords-in-a-net-app-config-file](http://weblogs.asp.net/jongalloway/encrypting-passwords-in-a-net-app-config-file)

<pre><code>
static byte[] entropy = System.Text.Encoding.Unicode.GetBytes("Salt Is Not A Password");
</code></pre>

<pre><code>
public static string EncryptString(System.Security.SecureString input)
{
    byte[] encryptedData = System.Security.Cryptography.ProtectedData.Protect(
        System.Text.Encoding.Unicode.GetBytes(ToInsecureString(input)),
        entropy,
        System.Security.Cryptography.DataProtectionScope.CurrentUser);
    return Convert.ToBase64String(encryptedData);
}
</code></pre>

<pre><code>
public static SecureString DecryptString(string encryptedData)
{
    try
    {
        byte[] decryptedData = System.Security.Cryptography.ProtectedData.Unprotect(
            Convert.FromBase64String(encryptedData),
            entropy,
            System.Security.Cryptography.DataProtectionScope.CurrentUser);
        return ToSecureString(System.Text.Encoding.Unicode.GetString(decryptedData));
    }
    catch
    {
        return new SecureString();
    }
}
</code></pre>
<pre><code>
public static SecureString ToSecureString(string input)
{
    SecureString secure = new SecureString();
    foreach (char c in input)
    {
        secure.AppendChar(c);
    }
    secure.MakeReadOnly();
    return secure;
}
</code></pre>
<pre><code>
public static string ToInsecureString(SecureString input)
{
    string returnValue = string.Empty;
    IntPtr ptr = System.Runtime.InteropServices.Marshal.SecureStringToBSTR(input);
    try
    {
        returnValue = System.Runtime.InteropServices.Marshal.PtrToStringBSTR(ptr);
    }
    finally
    {
        System.Runtime.InteropServices.Marshal.ZeroFreeBSTR(ptr);
    }
    return returnValue;
}
</code></pre>

<pre><code>
AppSettings.Password = EncryptString(ToSecureString(PasswordTextBox.Password));
</code></pre>


[Additional Resources] (http://www.builderau.com.au/program/dotnet/soa/Encrypting-NET-configuration-files-through-code/0,339028399,339281837,00.htm)

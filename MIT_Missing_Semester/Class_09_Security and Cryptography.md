---
date: 2025-11-15
---
# Security and Cryptography  
安全与密码学

Last year’s [security and privacy lecture](https://missing.csail.mit.edu/2019/security/) focused on how you can be more secure as a computer _user_. This year, we will focus on security and cryptography concepts that are relevant in understanding tools covered earlier in this class, such as the use of hash functions in Git or key derivation functions and symmetric/asymmetric cryptosystems in SSH.  
去年的[安全与隐私讲座](https://missing.csail.mit.edu/2019/security/)重点讲解了如何提高计算机_用户_的安全性。今年，我们将重点讲解与理解本课程前面提到的工具相关的安全和密码学概念，例如 Git 中的哈希函数或 SSH 中的密钥派生函数和对称/非对称密码系统。

This lecture is not a substitute for a more rigorous and complete course on computer systems security ([6.858](https://css.csail.mit.edu/6.858/)) or cryptography ([6.857](https://courses.csail.mit.edu/6.857/) and 6.875). Don’t do security work without formal training in security. Unless you’re an expert, don’t [roll your own crypto](https://www.schneier.com/blog/archives/2015/05/amateurs_produc.html). The same principle applies to systems security.  
本次讲座不能替代更严谨、更完整的计算机系统安全（ [6.858](https://css.csail.mit.edu/6.858/) ）或密码学（ [6.857](https://courses.csail.mit.edu/6.857/) 和 6.875）课程。切勿在未接受过正规安全培训的情况下从事安全工作。除非您是专家，否则请勿[自行编写加密程序](https://www.schneier.com/blog/archives/2015/05/amateurs_produc.html) 。同样的原则也适用于系统安全。

This lecture has a very informal (but we think practical) treatment of basic cryptography concepts. This lecture won’t be enough to teach you how to _design_ secure systems or cryptographic protocols, but we hope it will be enough to give you a general understanding of the programs and protocols you already use.  
本次讲座以非常非正式（但我们认为很实用）的方式讲解基础知识 密码学概念。本次讲座不足以教会你如何…… 虽然_我们无法设计_安全系统或加密协议，但我们希望这足以让您对已经使用的程序和协议有一个大致的了解。

# Entropy  熵

[Entropy](https://en.wikipedia.org/wiki/Entropy_(information_theory)) is a measure of randomness. This is useful, for example, when determining the strength of a password.  
[熵](https://en.wikipedia.org/wiki/Entropy_(information_theory))是衡量随机性的指标。例如，在确定密码强度时，熵就很有用。

![XKCD 936: Password Strength](https://imgs.xkcd.com/comics/password_strength.png)

As the above [XKCD comic](https://xkcd.com/936/) illustrates, a password like “correcthorsebatterystaple” is more secure than one like “Tr0ub4dor&3”. But how do you quantify something like this?  
正如上面的 [XKCD 漫画](https://xkcd.com/936/)所示，像“correcthorsebatterystaple”这样的密码比像“Tr0ub4dor&3”这样的密码更安全。但是，如何量化这种差异呢？

Entropy is measured in _bits_, and when selecting uniformly at random from a set of possible outcomes, the entropy is equal to `log_2(# of possibilities)`. A fair coin flip gives 1 bit of entropy. A dice roll (of a 6-sided die) has ~2.58 bits of entropy.  
熵以_比特为单位_进行测量。从一组可能的结果中均匀随机选择时，熵等于 `log_2(# of possibilities)` 。抛一枚均匀的硬币会产生 1 比特的熵。掷一枚骰子（六面骰）会产生约 2.58 比特的熵。

You should consider that the attacker knows the _model_ of the password, but not the randomness (e.g. from [dice rolls](https://en.wikipedia.org/wiki/Diceware)) used to select a particular password.  
你应该考虑到攻击者知道密码的_模型_ ，但不知道用于选择特定密码的随机性（例如通过[掷骰子](https://en.wikipedia.org/wiki/Diceware) ）。

How many bits of entropy is enough? It depends on your threat model. For online guessing, as the XKCD comic points out, ~40 bits of entropy is pretty good. To be resistant to offline guessing, a stronger password would be necessary (e.g. 80 bits, or more).  
多少比特的熵才足够？这取决于你的威胁模型。正如 XKCD 漫画所指出的，对于在线猜测，大约 40 比特的熵就足够了。为了抵御离线猜测，则需要更强的密码（例如 80 比特或更多）。

# Hash functions  哈希函数

A [cryptographic hash function](https://en.wikipedia.org/wiki/Cryptographic_hash_function) maps data of arbitrary size to a fixed size, and has some special properties. A rough specification of a hash function is as follows:  
[加密哈希函数](https://en.wikipedia.org/wiki/Cryptographic_hash_function)将任意大小的数据映射到固定大小的数据，并具有一些特殊性质。哈希函数的粗略定义如下：

```
hash(value: array<byte>) -> vector<byte, N>  (for some fixed N)
```

An example of a hash function is [SHA1](https://en.wikipedia.org/wiki/SHA-1), which is used in Git. It maps arbitrary-sized inputs to 160-bit outputs (which can be represented as 40 hexadecimal characters). We can try out the SHA1 hash on an input using the `sha1sum` command:  
哈希函数的一个例子是 [SHA1](https://en.wikipedia.org/wiki/SHA-1) ，它在 Git 中使用。它将任意长度的输入映射到 160 位输出（可以表示为 40 个十六进制字符）。我们可以使用 `sha1sum` 命令对输入进行 SHA1 哈希运算：

```
$ printf 'hello' | sha1sum
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
$ printf 'hello' | sha1sum
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
$ printf 'Hello' | sha1sum 
f7ff9e8b7bb2e09b70935a5d785e0cc5d9d0abf0
```

At a high level, a hash function can be thought of as a hard-to-invert random-looking (but deterministic) function (and this is the [ideal model of a hash function](https://en.wikipedia.org/wiki/Random_oracle)). A hash function has the following properties:  
从宏观层面来看，哈希函数可以被视为一个难以逆向的、看似随机（实则确定）的函数（这是[哈希函数的理想模型](https://en.wikipedia.org/wiki/Random_oracle) ）。哈希函数具有以下特性：

-   Deterministic: the same input always generates the same output.  
    确定性：相同的输入总是产生相同的输出。
-   Non-invertible: it is hard to find an input `m` such that `hash(m) = h` for some desired output `h`.  
    不可逆：很难找到一个输入 `m` 使得对于某个期望的输出 `h` `hash(m) = h` 。
-   Target collision resistant: given an input `m_1`, it’s hard to find a different input `m_2` such that `hash(m_1) = hash(m_2)`.  
    目标抗碰撞性：给定一个输入 `m_1` ，很难找到一个不同的输入 `m_2` ，使得 `hash(m_1) = hash(m_2)` 。
-   Collision resistant: it’s hard to find two inputs `m_1` and `m_2` such that `hash(m_1) = hash(m_2)` (note that this is a strictly stronger property than target collision resistance).  
    抗碰撞性：很难找到两个输入 `m_1` 和 `m_2` ，使得 `hash(m_1) = hash(m_2)` （请注意，这是一个比目标碰撞抵抗性更强的属性）。

Note: while it may work for certain purposes, SHA-1 is [no longer](https://shattered.io/) considered a strong cryptographic hash function. You might find this table of [lifetimes of cryptographic hash functions](https://valerieaurora.org/hash.html) interesting. However, note that recommending specific hash functions is beyond the scope of this lecture. If you are doing work where this matters, you need formal training in security/cryptography.  
注意：虽然 SHA-1 在某些情况下可能有效，但它[已不再](https://shattered.io/)被认为是一种强大的加密哈希函数。您或许会对这张[加密哈希函数生命周期](https://valerieaurora.org/hash.html)表感兴趣。然而，请注意，推荐具体的哈希函数超出了本次讲座的范围。如果您从事的工作涉及这方面的知识，则需要接受安全/密码学方面的正规培训。

## Applications  应用程序

-   Git, for content-addressed storage. The idea of a [hash function](https://en.wikipedia.org/wiki/Hash_function) is a more general concept (there are non-cryptographic hash functions). Why does Git use a cryptographic hash function?  
    Git 用于内容寻址存储。 [哈希函数](https://en.wikipedia.org/wiki/Hash_function)的概念更为通用（存在非加密哈希函数）。为什么 Git 使用加密哈希函数呢？
-   A short summary of the contents of a file. Software can often be downloaded from (potentially less trustworthy) mirrors, e.g. Linux ISOs, and it would be nice to not have to trust them. The official sites usually post hashes alongside the download links (that point to third-party mirrors), so that the hash can be checked after downloading a file.  
    文件内容的简要概述。软件通常可以从（可能不太可靠的）镜像站点下载，例如 Linux ISO 镜像，但如果不必信任这些站点就更好了。官方网站通常会在下载链接（指向第三方镜像站点）旁边发布哈希值，以便在下载文件后检查哈希值。
-   [Commitment schemes](https://en.wikipedia.org/wiki/Commitment_scheme). Suppose you want to commit to a particular value, but reveal the value itself later. For example, I want to do a fair coin toss “in my head”, without a trusted shared coin that two parties can see. I could choose a value `r = random()`, and then share `h = sha256(r)`. Then, you could call heads or tails (we’ll agree that even `r` means heads, and odd `r` means tails). After you call, I can reveal my value `r`, and you can confirm that I haven’t cheated by checking `sha256(r)` matches the hash I shared earlier.  
    [承诺方案](https://en.wikipedia.org/wiki/Commitment_scheme) 。假设你想承诺一个特定的值，但稍后再透露这个值本身。例如，我想“在心里”进行一次公平的抛硬币，而无需双方都能看到的受信任的共享硬币。我可以随机选择一个值 `r = random()` ，然后分享 `h = sha256(r)` 。之后，你可以调用“正面”或“反面”（我们约定偶数 `r` 表示正面，奇数 `r` 表示反面）。在你调用之后，我可以透露我的值 `r` ，你可以通过检查 `sha256(r)` 是否与我之前分享的哈希值匹配来确认我没有作弊。

# Key derivation functions  
关键推导函数

A related concept to cryptographic hashes, [key derivation functions](https://en.wikipedia.org/wiki/Key_derivation_function) (KDFs) are used for a number of applications, including producing fixed-length output for use as keys in other cryptographic algorithms. Usually, KDFs are deliberately slow, in order to slow down offline brute-force attacks.  
密钥[派生函数](https://en.wikipedia.org/wiki/Key_derivation_function) (KDF) 与加密哈希密切相关，它被用于多种应用，包括生成固定长度的输出，用作其他加密算法的密钥。通常，KDF 的计算速度故意设置得很慢，以减缓离线暴力破解攻击。

## Applications  应用程序

-   Producing keys from passphrases for use in other cryptographic algorithms (e.g. symmetric cryptography, see below).  
    从口令生成密钥，用于其他加密算法（例如对称加密，见下文）。
-   Storing login credentials. Storing plaintext passwords is bad; the right approach is to generate and store a random [salt](https://en.wikipedia.org/wiki/Salt_(cryptography)) `salt = random()` for each user, store `KDF(password + salt)`, and verify login attempts by re-computing the KDF given the entered password and the stored salt.  
    存储登录凭据。以明文形式存储密码是不好的；正确的做法是使用加密方式。 方法是生成并存储一个随机数 [salt](https://en.wikipedia.org/wiki/Salt_(cryptography)) `salt = random()` 对于每个用户，存储 `KDF(password + salt)` ，并通过根据输入的密码和存储的 salt 重新计算 KDF 来验证登录尝试。

# Symmetric cryptography  对称密码学

Hiding message contents is probably the first concept you think about when you think about cryptography. Symmetric cryptography accomplishes this with the following set of functionality:  
提到密码学，你首先想到的可能就是隐藏消息内容。对称密码学通过以下功能实现这一目标：

```
keygen() -> key  (this function is randomized)

encrypt(plaintext: array<byte>, key) -> array<byte>  (the ciphertext)
decrypt(ciphertext: array<byte>, key) -> array<byte>  (the plaintext)
```

The encrypt function has the property that given the output (ciphertext), it’s hard to determine the input (plaintext) without the key. The decrypt function has the obvious correctness property, that `decrypt(encrypt(m, k), k) = m`.  
加密函数具有这样的性质：给定输出（密文），在没有密钥的情况下很难确定输入（明文）。解密函数具有显而易见的正确性性质，即 `decrypt(encrypt(m, k), k) = m` 。

An example of a symmetric cryptosystem in wide use today is [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).  
当今广泛使用的对称密码系统示例是： [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 。

## Applications  应用程序

-   Encrypting files for storage in an untrusted cloud service. This can be combined with KDFs, so you can encrypt a file with a passphrase. Generate `key = KDF(passphrase)`, and then store `encrypt(file, key)`.  
    对存储在不受信任的云服务中的文件进行加密。这可以与密钥派生函数 (KDF) 结合使用，因此您可以使用密码短语加密文件。生成 `key = KDF(passphrase)` ，然后存储 `encrypt(file, key)` 。

# Asymmetric cryptography  非对称密码学

The term “asymmetric” refers to there being two keys, with two different roles. A private key, as its name implies, is meant to be kept private, while the public key can be publicly shared and it won’t affect security (unlike sharing the key in a symmetric cryptosystem). Asymmetric cryptosystems provide the following set of functionality, to encrypt/decrypt and to sign/verify:  
“非对称”一词指的是存在两个密钥，它们各自扮演着不同的角色。私钥顾名思义，需要保密，而公钥可以公开共享，并且不会影响安全性（这与对称密码系统中共享密钥的情况不同）。非对称密码系统提供以下功能：加密/解密和签名/验证：

```
keygen() -> (public key, private key)  (this function is randomized)

encrypt(plaintext: array<byte>, public key) -> array<byte>  (the ciphertext)
decrypt(ciphertext: array<byte>, private key) -> array<byte>  (the plaintext)

sign(message: array<byte>, private key) -> array<byte>  (the signature)
verify(message: array<byte>, signature: array<byte>, public key) -> bool  (whether or not the signature is valid)
```

The encrypt/decrypt functions have properties similar to their analogs from symmetric cryptosystems. A message can be encrypted using the _public_ key. Given the output (ciphertext), it’s hard to determine the input (plaintext) without the _private_ key. The decrypt function has the obvious correctness property, that `decrypt(encrypt(m, public key), private key) = m`.  
加密/解密函数具有与对称密码系统中的对应函数类似的性质。可以使用_公钥加密_消息。给定输出（密文），如果没有_私钥_ ，很难确定输入（明文）。解密函数具有显而易见的正确性，即 `decrypt(encrypt(m, public key), private key) = m` 。

Symmetric and asymmetric encryption can be compared to physical locks. A symmetric cryptosystem is like a door lock: anyone with the key can lock and unlock it. Asymmetric encryption is like a padlock with a key. You could give the unlocked lock to someone (the public key), they could put a message in a box and then put the lock on, and after that, only you could open the lock because you kept the key (the private key).  
对称加密和非对称加密可以比作物理锁。对称加密系统就像一把门锁：任何拥有钥匙的人都可以上锁和解锁。非对称加密则像一把带钥匙的挂锁。你可以把未上锁的挂锁（公钥）交给别人，他们可以把信息放在盒子里，然后锁上，之后只有你能打开这把锁，因为你持有钥匙（私钥）。

The sign/verify functions have the same properties that you would hope physical signatures would have, in that it’s hard to forge a signature. No matter the message, without the _private_ key, it’s hard to produce a signature such that `verify(message, signature, public key)` returns true. And of course, the verify function has the obvious correctness property that `verify(message, sign(message, private key), public key) = true`.  
签名/验证功能具有与物理签名相同的特性，即难以伪造签名。无论消息内容如何，​​如果没有_私钥_ ，都很难生成一个有效的签名。 `verify(message, signature, public key)` 返回 true。当然，验证函数具有显而易见的正确性属性 `verify(message, sign(message, private key), public key) = true` 。

## Applications  应用程序

-   [PGP email encryption](https://en.wikipedia.org/wiki/Pretty_Good_Privacy). People can have their public keys posted online (e.g. in a PGP keyserver, or on [Keybase](https://keybase.io/)). Anyone can send them encrypted email.  
    [PGP 电子邮件加密](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) 。 人们可以将自己的公钥发布到网上（例如在 PGP 密钥服务器上，或在网上）。 [Keybase](https://keybase.io/) ）。任何人都可以向他们发送加密电子邮件。
-   Private messaging. Apps like [Signal](https://signal.org/) and [Keybase](https://keybase.io/) use asymmetric keys to establish private communication channels.  
    私信应用，例如 [Signal](https://signal.org/) 和 [Keybase](https://keybase.io/) 使用非对称密钥建立私有通信通道。
-   Signing software. Git can have GPG-signed commits and tags. With a posted public key, anyone can verify the authenticity of downloaded software.  
    软件签名。Git 可以对提交和标签进行 GPG 签名。通过公开的公钥，任何人都可以验证下载软件的真实性。

## Key distribution  关键分发

Asymmetric-key cryptography is wonderful, but it has a big challenge of distributing public keys / mapping public keys to real-world identities. There are many solutions to this problem. Signal has one simple solution: trust on first use, and support out-of-band public key exchange (you verify your friends’ “safety numbers” in person). PGP has a different solution, which is [web of trust](https://en.wikipedia.org/wiki/Web_of_trust). Keybase has yet another solution of [social proof](https://keybase.io/blog/chat-apps-softer-than-tofu) (along with other neat ideas). Each model has its merits; we (the instructors) like Keybase’s model.  
非对称密钥加密技术很棒，但它面临着一个巨大的挑战： 分发公钥/将公钥映射到现实世界的身份。 这个问题有很多解决方案。Signal 的解决方案很简单：信任。 首次使用，并支持带外公钥交换（您验证您的 朋友的“安全号码”（当面告知）。PGP 提供了一种不同的解决方案，即： [信任网络](https://en.wikipedia.org/wiki/Web_of_trust) 。Keybase 提供了另一种[社会认同](https://keybase.io/blog/chat-apps-softer-than-tofu)解决方案（以及其他一些巧妙的想法）。每种模型都有其优点；我们（讲师们）更喜欢 Keybase 的模型。

# Case studies  案例研究
## Password managers  密码管理器

This is an essential tool that everyone should try to use (e.g. [KeePassXC](https://keepassxc.org/), [pass](https://git.zx2c4.com/password-store/about/), and [1Password](https://1password.com)). Password managers make it convenient to use unique, randomly generated high-entropy passwords for all your logins, and they save all your passwords in one place, encrypted with a symmetric cipher with a key produced from a passphrase using a KDF.  
这是每个人都应该尝试使用的重要工具（例如）。 [KeePassXC](https://keepassxc.org/) 、 [pass](https://git.zx2c4.com/password-store/about/) 和 [1Password](https://1password.com) 等密码管理器让您能够方便地为所有登录账户使用唯一、随机生成的高熵密码，并将所有密码保存在一个地方，使用对称密码进行加密，密钥由使用 KDF 的密码短语生成。

Using a password manager lets you avoid password reuse (so you’re less impacted when websites get compromised), use high-entropy passwords (so you’re less likely to get compromised), and only need to remember a single high-entropy password.  
使用密码管理器可以避免重复使用密码（这样当网站遭到入侵时，你受到的影响会更小），使用高熵密码（这样你被入侵的可能性会更小），而且你只需要记住一个高熵密码。

## Two-factor authentication  
双因素身份验证

[Two-factor authentication](https://en.wikipedia.org/wiki/Multi-factor_authentication) (2FA) requires you to use a passphrase (“something you know”) along with a 2FA authenticator (like a [YubiKey](https://www.yubico.com/), “something you have”) in order to protect against stolen passwords and [phishing](https://en.wikipedia.org/wiki/Phishing) attacks.  
[双因素身份验证](https://en.wikipedia.org/wiki/Multi-factor_authentication) 双因素身份验证 (2FA) 要求您使用密码短语（“您知道的信息”）以及双因素身份验证。 身份验证器（例如 [YubiKey](https://www.yubico.com/) ，“你拥有的东西”） 为了防止密码被盗和 [网络钓鱼](https://en.wikipedia.org/wiki/Phishing)攻击。

## Full disk encryption  全盘加密

Keeping your laptop’s entire disk encrypted is an easy way to protect your data in the case that your laptop is stolen. You can use [cryptsetup + LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_a_non-root_file_system) on Linux, [BitLocker](https://fossbytes.com/enable-full-disk-encryption-windows-10/) on Windows, or [FileVault](https://support.apple.com/en-us/HT204837) on macOS. This encrypts the entire disk with a symmetric cipher, with a key protected by a passphrase.  
将笔记本电脑的整个磁盘加密是保护数据的一种简单方法，以防笔记本电脑被盗。您可以使用 [cryptsetup + LUKS。](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_a_non-root_file_system) 在 Linux 系统上， Windows 系统上的 [BitLocker](https://fossbytes.com/enable-full-disk-encryption-windows-10/) 或 macOS 系统上的 [FileVault](https://support.apple.com/en-us/HT204837) ，都会使用对称加密算法对整个磁盘进行加密，密钥由密码保护。

## Private messaging  私信

Use [Signal](https://signal.org/) or [Keybase](https://keybase.io/). End-to-end security is bootstrapped from asymmetric-key encryption. Obtaining your contacts’ public keys is the critical step here. If you want good security, you need to authenticate public keys out-of-band (with Signal or Keybase), or trust social proofs (with Keybase).  
使用 [Signal](https://signal.org/) 或 [Keybase](https://keybase.io/) 。端到端安全基于非对称密钥加密。获取联系人的公钥是关键步骤。为了确保良好的安全性，您需要通过带外方式（使用 Signal 或 Keybase）验证公钥，或者信任社交证明（使用 Keybase）。

## SSH

We’ve covered the use of SSH and SSH keys in an [earlier lecture](https://missing.csail.mit.edu/2020/command-line/#remote-machines). Let’s look at the cryptography aspects of this.  
我们在[之前的课程](https://missing.csail.mit.edu/2020/command-line/#remote-machines)中已经讲解过 SSH 和 SSH 密钥的使用。现在让我们来看看其中的密码学方面内容。

When you run `ssh-keygen`, it generates an asymmetric key pair, `public_key, private_key`. This is generated randomly, using entropy provided by the operating system (collected from hardware events, etc.). The public key is stored as-is (it’s public, so keeping it a secret is not important), but at rest, the private key should be encrypted on disk. The `ssh-keygen` program prompts the user for a passphrase, and this is fed through a key derivation function to produce a key, which is then used to encrypt the private key with a symmetric cipher.  
运行 `ssh-keygen` 时，它会生成一对非对称密钥对： `public_key, private_key` )。该密钥对是随机生成的，使用操作系统提供的熵（从硬件事件等收集）。公钥会直接存储（因为它是公开的，所以保密并不重要），但私钥在磁盘上存储时应该加密。ssh `ssh-keygen` 程序会提示用户输入密码，然后通过密钥派生函数生成一个密钥，该密钥随后用于使用对称加密算法对私钥进行加密。

In use, once the server knows the client’s public key (stored in the `.ssh/authorized_keys` file), a connecting client can prove its identity using asymmetric signatures. This is done through [challenge-response](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication). At a high level, the server picks a random number and sends it to the client. The client then signs this message and sends the signature back to the server, which checks the signature against the public key on record. This effectively proves that the client is in possession of the private key corresponding to the public key that’s in the server’s `.ssh/authorized_keys` file, so the server can allow the client to log in.  
使用时，一旦服务器知道客户端的公钥（存储在……中）， `.ssh/authorized_keys` 文件），连接的客户端可以使用该文件证明其身份。 非对称签名。这是通过以下方式实现的： [挑战-响应机制](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication) 。简而言之，服务器生成一个随机数并发送给客户端。客户端对该消息进行签名，并将签名发送回服务器。服务器将签名与记录在案的公钥进行比对。这有效地证明了客户端拥有与服务器 \` `.ssh/authorized_keys` 文件中的公钥对应的私钥，因此服务器可以允许客户端登录。

# Resources  资源

-   [Last year’s notes](https://missing.csail.mit.edu/2019/security/): from when this lecture was more focused on security and privacy as a computer user  
    [去年的笔记](https://missing.csail.mit.edu/2019/security/) ：当时这门课更侧重于计算机用户的安全和隐私问题。
-   [Cryptographic Right Answers](https://latacora.micro.blog/2018/04/03/cryptographic-right-answers.html): answers “what crypto should I use for X?” for many common X.  
    [密码学正确答案](https://latacora.micro.blog/2018/04/03/cryptographic-right-answers.html) ：回答“对于许多常见的 X，我应该使用哪种加密方式？”

# Exercises  练习

1.  **Entropy.  熵。**
    1.  Suppose a password is chosen as a concatenation of four lower-case dictionary words, where each word is selected uniformly at random from a dictionary of size 100,000. An example of such a password is `correcthorsebatterystaple`. How many bits of entropy does this have?  
        假设密码由四个小写字母连接而成 字典词汇，其中每个词都是从字典中随机均匀选取的。 一个大小为 100,000 的字典。这样的密码示例是： `correcthorsebatterystaple` 。这有多少比特的熵？
    2.  Consider an alternative scheme where a password is chosen as a sequence of 8 random alphanumeric characters (including both lower-case and upper-case letters). An example is `rg8Ql34g`. How many bits of entropy does this have?  
        考虑另一种方案，其中密码选择为 8 个随机字母数字字符（包括大小写字母）的序列。例如 `rg8Ql34g` 。这种方案有多少比特的熵？
    3.  Which is the stronger password?  
        哪个密码更安全？
    4.  Suppose an attacker can try guessing 10,000 passwords per second. On average, how long will it take to break each of the passwords?  
        假设攻击者每秒可以尝试猜测 10,000 个密码。平均而言，破解每个密码需要多长时间？
2.  **Cryptographic hash functions.** Download a Debian image from a [mirror](https://www.debian.org/CD/http-ftp/) (e.g. [from this Argentinean mirror](http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/)). Cross-check the hash (e.g. using the `sha256sum` command) with the hash retrieved from the official Debian site (e.g. [this file](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS) hosted at `debian.org`, if you’ve downloaded the linked file from the Argentinean mirror).  
    **加密哈希函数。** 从以下位置下载 Debian 镜像： [镜像](https://www.debian.org/CD/http-ftp/) （例如[来自这个阿根廷镜像](http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/) ）。将哈希值（例如使用 `sha256sum` 命令）与从 Debian 官方网站（例如[此文件）](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS) 获取的哈希值进行交叉验证。 托管在 `debian.org` 上（如果您已从阿根廷镜像下载了链接文件）。
3.  **Symmetric cryptography.** Encrypt a file with AES encryption, using [OpenSSL](https://www.openssl.org/): `openssl aes-256-cbc -salt -in {input filename} -out {output filename}`. Look at the contents using `cat` or `hexdump`. Decrypt it with `openssl aes-256-cbc -d -in {input filename} -out {output filename}` and confirm that the contents match the original using `cmp`.  
    **对称加密。** 使用 AES 加密算法加密文件。 [OpenSSL](https://www.openssl.org/) ： `openssl aes-256-cbc -salt -in {input filename} -out {output filename}` 。使用 `cat` 查看内容或 使用 `hexdump` 进行解密，并使用 `openssl aes-256-cbc -d -in {input filename} -out {output filename}` 确认其内容与原始内容匹配。 `cmp` 。
4.  **Asymmetric cryptography.  
    非对称密码学。**
    1.  Set up [SSH keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2) on a computer you have access to (not Athena, because Kerberos interacts weirdly with SSH keys). Make sure your private key is encrypted with a passphrase, so it is protected at rest.  
        设置 [SSH 密钥](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2) 在你有权限访问的计算机上（不是 Athena，因为 Kerberos 会与之交互） 奇怪的是，SSH 密钥也会出现问题）。确保 您的私钥使用密码短语加密，因此受到保护。 休息。
    2.  [Set up GPG  设置 GPG](https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages)
    3.  Send Anish an encrypted email ([public key](https://keybase.io/anish)).  
        向 Anish 发送一封加密电子邮件（ [公钥](https://keybase.io/anish) ）。
    4.  Sign a Git commit with `git commit -S` or create a signed Git tag with `git tag -s`. Verify the signature on the commit with `git show --show-signature` or on the tag with `git tag -v`.  
        使用 `git commit -S` 创建已签名的 Git 标签。 `git tag -s` . 验证提交的签名，使用 `git show --show-signature` 提交的签名，或使用 `git tag -v` 验证标签的签名。

* * *

# Beland Auth Crypto Lib

[![NPM version](https://badge.fury.io/js/beland-crypto.svg)](https://npmjs.org/package/beland-crypto@latest)
[![Install Size](https://packagephobia.now.sh/badge?p=beland-crypto@latest)](https://packagephobia.now.sh/result?p=beland-crypto@latest)

## How to Install

```bash
  npm i beland-crypto
```

## Create a new Identity using Ethers

```typescript
import type { AuthChain, AuthIdentity } from 'beland-crypto/dist/types'
import Authenticator from 'beland-crypto/dist/Authenticator'
import { Wallet } from '@ethersproject/wallet'
import { Web3Provider, ExternalProvider } from '@ethersproject/providers'

/**
 *
 * @params provider - any ethereum provider (e.g: window.ethereum)
 * @params expiration - ttl in seconds of the identity
 */
export async function createIdentity(provider: ExternalProvider, expiration: number): Promise<AuthIdentity> {
  const signer = new Web3Provider(provider).getSigner()
  const address = await signer.getAddress()

  const wallet = Wallet.createRandom()
  const payload = {
    address: wallet.address,
    privateKey: wallet.privateKey,
    publicKey: wallet.publicKey,
  }

  const identity = await Authenticator.initializeAuthChain(
    address,
    payload,
    expiration,
    (message) => new Web3Provider(provider).getSigner().signMessage(message)
  )

  return identity
}
```

## Create a new Identity using Web3x

```typescript
import type { AuthChain, AuthIdentity } from 'beland-crypto/dist/types'
import Authenticator from 'beland-crypto/dist/Authenticator'
import { Eth } from 'web3x/eth'
import { Address } from 'web3x/address'
import { Account } from 'web3x/account'
import { Personal } from 'web3x/personal'
import { EthereumProvider } from 'web3x/providers'
import { bufferToHex } from 'web3x/utils/hex-buffer'

/**
 *
 * @params provider - any ethereum provider (e.g: window.ethereum)
 * @params expiration - ttl in seconds of the identity
 */
export async function createIdentity(provider: EthereumProvider, expiration: number): Promise<AuthIdentity> {
  const eth = new Eth(provider)
  const addresses = await eth.getAccounts()

  const provider = connection.provider
  const account = Account.create()
  const payload = {
    address: account.address.toString(),
    publicKey: bufferToHex(account.publicKey),
    privateKey: bufferToHex(account.privateKey),
  }

  const identity = await Authenticator.initializeAuthChain(
    addresses[0],
    payload,
    expiration,
    (message) =>
      new Personal(provider as any).sign(
        message,
        Address.fromString(address),
        ''
      )
  )

  return identity
}
```

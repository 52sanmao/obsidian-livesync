<script lang="ts">
    import DialogHeader from "@/lib/src/UI/components/DialogHeader.svelte";
    import Guidance from "@/lib/src/UI/components/Guidance.svelte";
    import Decision from "@/lib/src/UI/components/Decision.svelte";
    import UserDecisions from "@/lib/src/UI/components/UserDecisions.svelte";
    import InfoNote from "@/lib/src/UI/components/InfoNote.svelte";
    import ExtraItems from "@/lib/src/UI/components/ExtraItems.svelte";
    import InputRow from "@/lib/src/UI/components/InputRow.svelte";
    import Password from "@/lib/src/UI/components/Password.svelte";
    import {
        DEFAULT_SETTINGS,
        E2EEAlgorithmNames,
        E2EEAlgorithms,
        type EncryptionSettings,
    } from "../../../../lib/src/common/types";
    import { onMount } from "svelte";
    import type { GuestDialogProps } from "../../../../lib/src/UI/svelteDialog";
    import { copyTo, pickEncryptionSettings } from "../../../../lib/src/common/utils";
    const TYPE_CANCELLED = "cancelled";
    type ResultType = typeof TYPE_CANCELLED | EncryptionSettings;
    type Props = GuestDialogProps<ResultType, EncryptionSettings>;
    const { setResult, getInitialData }: Props = $props();
    let default_encryption: EncryptionSettings = {
        encrypt: true,
        passphrase: "",
        E2EEAlgorithm: DEFAULT_SETTINGS.E2EEAlgorithm,
        usePathObfuscation: true,
    } as EncryptionSettings;

    let encryptionSettings = $state<EncryptionSettings>({ ...default_encryption });

    onMount(() => {
        if (getInitialData) {
            const initialData = getInitialData();
            if (initialData) {
                copyTo(initialData, encryptionSettings);
            }
        }
    });
    let e2eeValid = $derived.by(() => {
        if (!encryptionSettings.encrypt) return true;
        return encryptionSettings.passphrase.trim().length >= 1;
    });

    function commit() {
        setResult(pickEncryptionSettings(encryptionSettings));
    }
</script>

<DialogHeader title="Setup.RemoteE2EE.Title" />
<Guidance message="Setup.RemoteE2EE.Guidance" />
<InputRow label="Setup.RemoteE2EE.LabelEncryption">
    <input type="checkbox" bind:checked={encryptionSettings.encrypt} />
    <Password
        name="e2ee-passphrase"
        placeholder="Enter your passphrase"
        bind:value={encryptionSettings.passphrase}
        disabled={!encryptionSettings.encrypt}
        required={encryptionSettings.encrypt}
    />
</InputRow>
<InfoNote title="Setup.RemoteE2EE.StronglyRecommendedTitle" message="Setup.RemoteE2EE.StronglyRecommended" />
<InfoNote warning message="Setup.RemoteE2EE.WarningSameSetting" />
<InputRow label="Setup.RemoteE2EE.LabelObfuscate">
    <input
        type="checkbox"
        bind:checked={encryptionSettings.usePathObfuscation}
        disabled={!encryptionSettings.encrypt}
    />
</InputRow>
<InfoNote message="Setup.RemoteE2EE.ObfuscateExplanation" />

<ExtraItems title="Setup.RemoteE2EE.AdvancedTitle">
    <InputRow label="Encryption Algorithm">
        <select bind:value={encryptionSettings.E2EEAlgorithm} disabled={!encryptionSettings.encrypt}>
            {#each Object.values(E2EEAlgorithms) as alg}
                <option value={alg}>{E2EEAlgorithmNames[alg] ?? alg}</option>
            {/each}
        </select>
    </InputRow>
    <InfoNote>
        In most cases, you should stick with the default algorithm ({E2EEAlgorithmNames[
            DEFAULT_SETTINGS.E2EEAlgorithm
        ]}), This setting is only required if you have an existing Vault encrypted in a different format.
    </InfoNote>
    <InfoNote warning>
        Changing the encryption algorithm will prevent access to any data previously encrypted with a different
        algorithm. Ensure that all your devices are configured to use the same algorithm to maintain access to your
        data.
    </InfoNote>
</ExtraItems>

<InfoNote warning message="Setup.RemoteE2EE.ManualWarning" />

<UserDecisions>
    <Decision title="Setup.UseSetupURI.ButtonProceed" important disabled={!e2eeValid} commit={() => commit()} />
    <Decision title="Setup.UseSetupURI.ButtonCancel" commit={() => setResult(TYPE_CANCELLED)} />
</UserDecisions>

// App.js
import React, { useState, useEffect, createContext, useContext } from 'react';
import { initializeApp } from 'firebase/app';
import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from 'firebase/auth';
import { getFirestore, doc, getDoc, setDoc, collection, query, where, getDocs } from 'firebase/firestore';

// --- Firebase Configuration ---
// These will be populated by Canvas environment variables or default values.
// Ensure __firebase_config and __app_id are available in your environment.
const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const auth = getAuth(app);

// --- Auth Context ---
const AuthContext = createContext(null);

export const AuthProvider = ({ children }) => {
    const [currentUser, setCurrentUser] = useState(null);
    const [isAuthenticated, setIsAuthenticated] = useState(false);
    const [isLoadingAuth, setIsLoadingAuth] = useState(true);
    const [discordAccessToken, setDiscordAccessToken] = useState(null);
    const [discordUser, setDiscordUser] = useState(null);
    const [discordGuilds, setDiscordGuilds] = useState([]);
    const [firebaseUser, setFirebaseUser] = useState(null); // Firebase user object

    useEffect(() => {
        // Firebase Auth State Listener
        const unsubscribe = onAuthStateChanged(auth, async (user) => {
            if (user) {
                setFirebaseUser(user);
                // If we have a Firebase user, we can consider auth ready for Firestore
            } else {
                setFirebaseUser(null);
                // If no Firebase user, try anonymous sign-in if no custom token
                if (typeof __initial_auth_token === 'undefined') {
                    try {
                        await signInAnonymously(auth);
                        console.log("Signed in anonymously to Firebase.");
                    } catch (error) {
                        console.error("Error signing in anonymously:", error);
                    }
                }
            }
            setIsLoadingAuth(false); // Auth state is now known
        });

        // Attempt custom token sign-in if available
        const signInWithToken = async () => {
            if (typeof __initial_auth_token !== 'undefined' && !firebaseUser) {
                try {
                    await signInWithCustomToken(auth, __initial_auth_token);
                    console.log("Signed in with custom token.");
                } catch (error) {
                    console.error("Error signing in with custom token:", error);
                }
            }
        };
        signInWithToken();

        return () => unsubscribe();
    }, [firebaseUser]); // Re-run if firebaseUser changes

    // Discord OAuth handling
    useEffect(() => {
        const handleDiscordAuthCallback = async () => {
            const params = new URLSearchParams(window.location.search);
            const code = params.get('code');
            const state = params.get('state'); // For security, verify this matches a stored state

            if (code) {
                setIsLoadingAuth(true);
                try {
                    // Exchange code for token (this would ideally be a backend call for security)
                    // For this example, we'll do it client-side for simplicity, but be aware of security implications.
                    const tokenResponse = await fetch('https://discord.com/api/oauth2/token', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/x-www-form-urlencoded',
                        },
                        body: new URLSearchParams({
                            client_id: process.env.REACT_APP_DISCORD_CLIENT_ID,
                            client_secret: process.env.REACT_APP_DISCORD_CLIENT_SECRET, // NEVER EXPOSE IN REAL APP
                            grant_type: 'authorization_code',
                            code: code,
                            redirect_uri: process.env.REACT_APP_DISCORD_REDIRECT_URI,
                            scope: 'identify guilds',
                        }).toString(),
                    });

                    if (!tokenResponse.ok) {
                        const errorData = await tokenResponse.json();
                        console.error('Failed to exchange Discord code for token:', errorData);
                        throw new Error('Failed to exchange Discord code for token');
                    }

                    const tokenData = await tokenResponse.json();
                    setDiscordAccessToken(tokenData.access_token);

                    // Fetch Discord user info
                    const userResponse = await fetch('https://discord.com/api/users/@me', {
                        headers: {
                            Authorization: `Bearer ${tokenData.access_token}`,
                        },
                    });
                    const userData = await userResponse.json();
                    setDiscordUser(userData);

                    // Fetch Discord guilds (servers)
                    const guildsResponse = await fetch('https://discord.com/api/users/@me/guilds', {
                        headers: {
                            Authorization: `Bearer ${tokenData.access_token}`,
                        },
                    });
                    const guildsData = await guildsResponse.json();
                    // Filter for guilds where the user has administrator permission (bit 3 = 0x8)
                    const adminGuilds = guildsData.filter(guild => (guild.permissions & 0x8) === 0x8);
                    setDiscordGuilds(adminGuilds);

                    setCurrentUser(userData); // Set the current user to Discord user
                    setIsAuthenticated(true);

                    // Clean up URL
                    window.history.replaceState({}, document.title, window.location.pathname);

                } catch (error) {
                    console.error('Discord OAuth error:', error);
                    setIsAuthenticated(false);
                    setDiscordAccessToken(null);
                    setDiscordUser(null);
                    setDiscordGuilds([]);
                } finally {
                    setIsLoadingAuth(false);
                }
            }
        };

        handleDiscordAuthCallback();
    }, []);

    const loginWithDiscord = () => {
        const discordClientId = process.env.REACT_APP_DISCORD_CLIENT_ID;
        const redirectUri = encodeURIComponent(process.env.REACT_APP_DISCORD_REDIRECT_URI);
        const scope = encodeURIComponent('identify guilds'); // Request user info and guild list
        const discordAuthUrl = `https://discord.com/oauth2/authorize?client_id=${discordClientId}&redirect_uri=${redirectUri}&response_type=code&scope=${scope}`;
        window.location.href = discordAuthUrl;
    };

    const logout = () => {
        setCurrentUser(null);
        setIsAuthenticated(false);
        setDiscordAccessToken(null);
        setDiscordUser(null);
        setDiscordGuilds([]);
        // Sign out from Firebase if needed, though Discord is primary
        auth.signOut().then(() => console.log("Firebase signed out")).catch(e => console.error("Firebase sign out error:", e));
    };

    const value = {
        currentUser,
        isAuthenticated,
        isLoadingAuth,
        discordAccessToken,
        discordUser,
        discordGuilds,
        firebaseUser, // Expose Firebase user
        loginWithDiscord,
        logout,
        db, // Provide Firestore instance
        auth, // Provide Auth instance
        appId // Provide app ID
    };

    return (
        <AuthContext.Provider value={value}>
            {children}
        </AuthContext.Provider>
    );
};

export const useAuth = () => useContext(AuthContext);

// --- Dashboard Component ---
const Dashboard = () => {
    const { isAuthenticated, isLoadingAuth, discordUser, discordGuilds, loginWithDiscord } = useAuth();
    const [selectedGuildId, setSelectedGuildId] = useState(null);
    const [message, setMessage] = useState('');

    useEffect(() => {
        const urlParams = new URLSearchParams(window.location.search);
        const guildIdFromUrl = urlParams.get('guild_id');
        if (guildIdFromUrl) {
            setSelectedGuildId(guildIdFromUrl);
        }
    }, []);

    if (isLoadingAuth) {
        return (
            <div className="min-h-screen flex items-center justify-center bg-gray-900 text-white">
                <p>Loading authentication...</p>
            </div>
        );
    }

    if (!isAuthenticated) {
        return (
            <div className="min-h-screen flex flex-col items-center justify-center bg-gray-900 text-white p-4">
                <div className="bg-gray-800 p-8 rounded-lg shadow-xl text-center max-w-md w-full">
                    <h1 className="text-3xl font-bold mb-6 text-indigo-400">Welcome to Auto Role Bot Dashboard</h1>
                    <p className="text-gray-300 mb-8">Please log in with Discord to manage your server's auto-role settings.</p>
                    <button
                        onClick={loginWithDiscord}
                        className="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-6 rounded-lg transition duration-300 ease-in-out transform hover:scale-105 shadow-lg"
                    >
                        <svg className="inline-block w-5 h-5 mr-2 -mt-1" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fillRule="evenodd" d="M10 0C4.477 0 0 4.477 0 10c0 4.991 3.657 9.128 8.438 9.879l.073.01c.402.072.547-.174.547-.386v-1.517c-3.148.682-3.812-1.517-3.812-1.517-.514-1.303-1.25-1.65-1.25-1.65-1.02-.696.077-.682.077-.682 1.12.077 1.708 1.15 1.708 1.15.992 1.708 2.607 1.216 3.23.929.1-.72.386-1.216.703-1.496-2.47-.282-5.06-1.236-5.06-5.504 0-1.216.43-2.208 1.15-2.986-.1-.282-.47-.1.43-2.95 0 0 .929-.318 3.04.929.886-.248 1.83-.372 2.77-.372.94 0 1.884.124 2.77.372 2.11-1.247 3.04-.929 3.04-.929.9.077.53.182.43 2.95.72.778 1.15 1.77 1.15 2.986 0 4.28-2.59 5.218-5.06 5.504.386.33.703.992.703 2.008v2.95c0 .212.145.458.547.386l.073-.01C16.343 19.128 20 14.991 20 10c0-5.523-4.477-10-10-10z" clipRule="evenodd" /></svg>
                        Login with Discord
                    </button>
                </div>
            </div>
        );
    }

    return (
        <div className="min-h-screen bg-gray-900 text-white p-4 sm:p-8">
            <header className="flex flex-col sm:flex-row justify-between items-center mb-8 pb-4 border-b border-gray-700">
                <h1 className="text-4xl font-extrabold text-indigo-400 mb-4 sm:mb-0">
                    Auto Role Bot Dashboard
                </h1>
                <div className="flex items-center">
                    {discordUser && (
                        <span className="text-gray-300 mr-4 text-lg">
                            Welcome, {discordUser.username}#{discordUser.discriminator}
                        </span>
                    )}
                    <button
                        onClick={logout}
                        className="bg-red-600 hover:bg-red-700 text-white font-bold py-2 px-4 rounded-lg transition duration-300 ease-in-out shadow-md"
                    >
                        Logout
                    </button>
                </div>
            </header>

            {selectedGuildId ? (
                <ServerSettings guildId={selectedGuildId} onBack={() => setSelectedGuildId(null)} />
            ) : (
                <div className="max-w-4xl mx-auto">
                    <h2 className="text-3xl font-bold mb-6 text-gray-200">Your Servers (Admin Access)</h2>
                    {message && (
                        <div className="bg-blue-500 text-white p-3 rounded-md mb-4 flex items-center justify-between">
                            <span>{message}</span>
                            <button onClick={() => setMessage('')} className="text-white font-bold text-xl">&times;</button>
                        </div>
                    )}
                    {discordGuilds.length > 0 ? (
                        <ul className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
                            {discordGuilds.map(guild => (
                                <li key={guild.id} className="bg-gray-800 rounded-lg shadow-lg overflow-hidden transition duration-300 ease-in-out transform hover:scale-105">
                                    <button
                                        onClick={() => setSelectedGuildId(guild.id)}
                                        className="w-full text-left p-6 flex items-center space-x-4 hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-opacity-75"
                                    >
                                        {guild.icon ? (
                                            <img
                                                src={`https://cdn.discordapp.com/icons/${guild.id}/${guild.icon}.png?size=64`}
                                                alt={`${guild.name} icon`}
                                                className="w-16 h-16 rounded-full object-cover border-2 border-indigo-500"
                                            />
                                        ) : (
                                            <div className="w-16 h-16 rounded-full bg-indigo-700 flex items-center justify-center text-xl font-bold text-white">
                                                {guild.name.charAt(0)}
                                            </div>
                                        )}
                                        <span className="text-xl font-semibold text-gray-100">{guild.name}</span>
                                    </button>
                                </li>
                            ))}
                        </ul>
                    ) : (
                        <p className="text-gray-400 text-lg">No servers found where you have administrator permissions and the bot is present. Please invite the bot to your server and ensure you have admin rights.</p>
                    )}
                    <div className="mt-8 p-6 bg-gray-800 rounded-lg shadow-lg">
                        <h3 className="text-2xl font-bold text-gray-200 mb-4">Invite the Bot</h3>
                        <p className="text-gray-300 mb-4">
                            To add the bot to your server, use the following invite link. Make sure to grant it the necessary permissions (Manage Roles, Send Messages, Read Message History, View Channels).
                        </p>
                        <a
                            href={`https://discord.com/api/oauth2/authorize?client_id=${process.env.REACT_APP_DISCORD_CLIENT_ID}&permissions=268437504&scope=bot`}
                            target="_blank"
                            rel="noopener noreferrer"
                            className="inline-block bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-lg transition duration-300 ease-in-out transform hover:scale-105 shadow-lg"
                        >
                            Invite Bot to Your Server
                        </a>
                        <p className="text-sm text-gray-500 mt-2">
                            (Permissions: 268437504 includes: View Channels, Send Messages, Manage Roles)
                        </p>
                    </div>
                </div>
            )}
            <p className="text-center text-gray-500 text-sm mt-8">
                User ID: {firebaseUser?.uid || 'Not authenticated'}
            </p>
        </div>
    );
};

// --- Server Settings Component ---
const ServerSettings = ({ guildId, onBack }) => {
    const { discordAccessToken, db, appId, firebaseUser } = useAuth();
    const [roles, setRoles] = useState([]);
    const [channels, setChannels] = useState([]);
    const [selectedRoles, setSelectedRoles] = useState([]);
    const [selectedChannel, setSelectedChannel] = useState('');
    const [welcomeMessage, setWelcomeMessage] = useState('Welcome {user} to {guild}! You are the {member_count}th member.');
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState('');
    const [successMessage, setSuccessMessage] = useState('');
    const [guildName, setGuildName] = useState('');

    const guildSettingsRef = db.collection('guild_settings').document(String(guildId));

    useEffect(() => {
        const fetchGuildData = async () => {
            if (!discordAccessToken || !guildId || !firebaseUser) {
                setError("Authentication or guild information missing.");
                setLoading(false);
                return;
            }

            setLoading(true);
            setError('');
            setSuccessMessage('');

            try {
                // Fetch guild details (name)
                const guildResponse = await fetch(`https://discord.com/api/guilds/${guildId}`, {
                    headers: {
                        Authorization: `Bearer ${discordAccessToken}`,
                    },
                });
                if (!guildResponse.ok) throw new Error(`Failed to fetch guild details: ${guildResponse.statusText}`);
                const guildData = await guildResponse.json();
                setGuildName(guildData.name);

                // Fetch roles
                const rolesResponse = await fetch(`https://discord.com/api/guilds/${guildId}/roles`, {
                    headers: {
                        Authorization: `Bearer ${discordAccessToken}`,
                    },
                });
                if (!rolesResponse.ok) throw new Error(`Failed to fetch roles: ${rolesResponse.statusText}`);
                const rolesData = await rolesResponse.json();
                // Filter out @everyone role and sort by position (higher position = higher role)
                setRoles(rolesData.filter(role => role.id !== guildId).sort((a, b) => b.position - a.position));

                // Fetch channels
                const channelsResponse = await fetch(`https://discord.com/api/guilds/${guildId}/channels`, {
                    headers: {
                        Authorization: `Bearer ${discordAccessToken}`,
                    },
                });
                if (!channelsResponse.ok) throw new Error(`Failed to fetch channels: ${channelsResponse.statusText}`);
                const channelsData = await channelsResponse.json();
                // Filter for text channels
                setChannels(channelsData.filter(channel => channel.type === 0)); // 0 is GUILD_TEXT

                // Fetch existing settings from Firestore
                const docSnap = await getDoc(guildSettingsRef);
                if (docSnap.exists()) {
                    const data = docSnap.data();
                    setSelectedRoles(data.auto_roles || []);
                    setSelectedChannel(data.welcome_channel_id || '');
                    setWelcomeMessage(data.welcome_message || 'Welcome {user} to {guild}! You are the {member_count}th member.');
                } else {
                    console.log("No existing settings for this guild. Using defaults.");
                }
            } catch (err) {
                console.error("Error fetching guild data or settings:", err);
                setError(`Failed to load server settings: ${err.message}`);
            } finally {
                setLoading(false);
            }
        };

        fetchGuildData();
    }, [guildId, discordAccessToken, firebaseUser]); // Depend on guildId and auth state

    const handleRoleChange = (e) => {
        const { value, checked } = e.target;
        setSelectedRoles(prev => {
            if (checked) {
                // Add role if checked, enforce max 5
                if (prev.length < 5) {
                    return [...prev, value];
                } else {
                    setError("You can select a maximum of 5 auto roles.");
                    return prev; // Don't add if already 5
                }
            } else {
                // Remove role if unchecked
                return prev.filter(roleId => roleId !== value);
            }
        });
        setSuccessMessage(''); // Clear success message on change
        setError(''); // Clear error message on change
    };

    const handleSave = async () => {
        setLoading(true);
        setError('');
        setSuccessMessage('');

        try {
            await setDoc(guildSettingsRef, {
                auto_roles: selectedRoles,
                welcome_channel_id: selectedChannel,
                welcome_message: welcomeMessage,
                last_updated: new Date().toISOString(),
                updated_by_firebase_uid: firebaseUser.uid, // Store Firebase UID of updater
                updated_by_discord_id: firebaseUser.uid, // Assuming Discord ID is the Firebase UID if using custom tokens
            }, { merge: true }); // Use merge to only update specified fields

            setSuccessMessage("Settings saved successfully!");
        } catch (err) {
            console.error("Error saving settings:", err);
            setError(`Failed to save settings: ${err.message}`);
        } finally {
            setLoading(false);
        }
    };

    if (loading) {
        return (
            <div className="min-h-screen flex items-center justify-center bg-gray-900 text-white">
                <p>Loading server settings...</p>
            </div>
        );
    }

    return (
        <div className="max-w-4xl mx-auto bg-gray-800 p-6 sm:p-8 rounded-lg shadow-xl">
            <h2 className="text-3xl font-bold mb-6 text-indigo-400">
                Settings for {guildName || 'Selected Server'}
            </h2>

            {error && (
                <div className="bg-red-500 text-white p-3 rounded-md mb-4 flex items-center justify-between">
                    <span>{error}</span>
                    <button onClick={() => setError('')} className="text-white font-bold text-xl">&times;</button>
                </div>
            )}
            {successMessage && (
                <div className="bg-green-500 text-white p-3 rounded-md mb-4 flex items-center justify-between">
                    <span>{successMessage}</span>
                    <button onClick={() => setSuccessMessage('')} className="text-white font-bold text-xl">&times;</button>
                </div>
            )}

            <div className="mb-6">
                <label className="block text-gray-300 text-lg font-semibold mb-2">
                    Select Auto Roles (Max 5):
                </label>
                <div className="grid grid-cols-1 sm:grid-cols-2 gap-3 max-h-60 overflow-y-auto pr-2 custom-scrollbar">
                    {roles.map(role => (
                        <div key={role.id} className="flex items-center bg-gray-700 p-3 rounded-md">
                            <input
                                type="checkbox"
                                id={`role-${role.id}`}
                                value={role.id}
                                checked={selectedRoles.includes(role.id)}
                                onChange={handleRoleChange}
                                className="form-checkbox h-5 w-5 text-indigo-600 rounded focus:ring-indigo-500"
                                disabled={selectedRoles.length >= 5 && !selectedRoles.includes(role.id)}
                            />
                            <label htmlFor={`role-${role.id}`} className="ml-3 text-gray-200 text-base cursor-pointer">
                                {role.name}
                            </label>
                        </div>
                    ))}
                </div>
                <p className="text-sm text-gray-400 mt-2">
                    Currently selected: {selectedRoles.length} / 5
                </p>
            </div>

            <div className="mb-6">
                <label htmlFor="welcome-channel" className="block text-gray-300 text-lg font-semibold mb-2">
                    Welcome Message Channel:
                </label>
                <select
                    id="welcome-channel"
                    value={selectedChannel}
                    onChange={(e) => { setSelectedChannel(e.target.value); setSuccessMessage(''); setError(''); }}
                    className="w-full p-3 rounded-md bg-gray-700 border border-gray-600 text-gray-200 focus:ring-indigo-500 focus:border-indigo-500"
                >
                    <option value="">-- Select a channel --</option>
                    {channels.map(channel => (
                        <option key={channel.id} value={channel.id}>{channel.name}</option>
                    ))}
                </select>
            </div>

            <div className="mb-6">
                <label htmlFor="welcome-message" className="block text-gray-300 text-lg font-semibold mb-2">
                    Customize Welcome Message:
                </label>
                <textarea
                    id="welcome-message"
                    value={welcomeMessage}
                    onChange={(e) => { setWelcomeMessage(e.target.value); setSuccessMessage(''); setError(''); }}
                    rows="5"
                    className="w-full p-3 rounded-md bg-gray-700 border border-gray-600 text-gray-200 focus:ring-indigo-500 focus:border-indigo-500 resize-y"
                    placeholder="Welcome {user} to {guild}! You are the {member_count}th member."
                ></textarea>
                <p className="text-sm text-gray-400 mt-2">
                    Use placeholders: <code className="bg-gray-700 px-1 py-0.5 rounded text-indigo-300">{' {user} '}</code>,
                    <code className="bg-gray-700 px-1 py-0.5 rounded text-indigo-300">{' {guild} '}</code>,
                    <code className="bg-gray-700 px-1 py-0.5 rounded text-indigo-300">{' {member_count} '}</code>
                </p>
            </div>

            <div className="flex justify-end space-x-4 mt-8">
                <button
                    onClick={onBack}
                    className="bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-6 rounded-lg transition duration-300 ease-in-out shadow-md"
                >
                    Back to Servers
                </button>
                <button
                    onClick={handleSave}
                    disabled={loading}
                    className="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-6 rounded-lg transition duration-300 ease-in-out transform hover:scale-105 shadow-lg disabled:opacity-50 disabled:cursor-not-allowed"
                >
                    {loading ? 'Saving...' : 'Save Settings'}
                </button>
            </div>
        </div>
    );
};

// --- Main App Component ---
export default function App() {
    return (
        <AuthProvider>
            <Dashboard />
        </AuthProvider>
    );
}

